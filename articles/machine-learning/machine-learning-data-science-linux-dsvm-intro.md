<properties
    pageTitle="Effettuare il provisioning di dati Linux scienza Virtual Machine | Microsoft Azure"
    description="Configurare e creare una macchina virtuale di Linux dati scienza su Azure procedura analitica e lavorare risorse."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="bradsev" />

# <a name="provision-the-linux-data-science-virtual-machine"></a>Effettuare il provisioning di dati Linux scienza Virtual Machine

Linux dati scienza Virtual Machine è una macchina virtuale Azure fornito con un insieme di strumenti preinstallati. Questi strumenti sono usati per eseguire analitica dati e lavorare risorse. I componenti di software chiave inclusi sono:

- Microsoft R Server Developer Edition
- Distribuzione di Python anaconda (versioni 2.7 e 3.5), incluse le librerie di analisi dei dati
- JupyterHub - un server di blocco appunti Jupyter multiutente R, Python, vengono Julia di supporto
- Esplora archivi Azure
- Azure dell'interfaccia della riga di comando (CLI) per la gestione delle risorse Azure
- Database PostgresSQL
- Strumenti di apprendimento computer
    - [Calcolo rete Toolkit (CNTK)](https://github.com/Microsoft/CNTK): una profondità toolkit di software da Microsoft Research di apprendimento.
    - [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): un computer veloce formazione system che supporta le tecniche, ad esempio online, hashing, allreduce, riduzioni, learning2search, attivo e apprendimento interattivo.
    - [XGBoost](https://xgboost.readthedocs.org/en/latest/): uno strumento che fornisce l'implementazione di albero invece un incremento veloce e accurato.
    - [Rattle](http://rattle.togaware.com/) (la R Analytical strumento per informazioni su facilmente): uno strumento che consente di introduzione a analitica dati e computer R semplice con esplorazione dei dati basati sull'interfaccia di apprendimento e modellazione con generazione automatica di codice R.
- Azure SDK Java, Python, Node, trascrizione, PHP
- Raccolte in R e Python per usare in Azure apprendimento e altri servizi di Azure
- Editor (Eclisse, Emacs, gedit, vi) e gli strumenti di sviluppo

Eseguire una ricerca di scienze dati a altro comporta l'iterazione su una sequenza delle attività:

1. Ricerca, il caricamento e pre-elaborazione dei dati
2. Creazione e verifica dei modelli
3. Distribuzione di modelli per consumo nelle applicazioni intelligente

Scienziati dati utilizzano diversi strumenti per eseguire queste attività. Può essere molto tempo per trovare le versioni appropriate del software e per scaricare, compilare e installare queste versioni.

Linux dati scienza Virtual Machine può semplificare il carico di lavoro in modo sostanziale. Usarlo per iniziare un progetto analitica. Consente di allo svolgimento di attività in varie lingue, tra cui R, Python, SQL, Java e C++. Eclisse fornisce un IDE per sviluppare e testare il codice è facile da usare. Azure SDK incluso nella macchina virtuale consente di creare le applicazioni usando vari servizi su Linux per la piattaforma cloud Microsoft. Inoltre, è possibile accedere in altre lingue come trascrizione, Perl, PHP e Node anche preinstallato.

Non esistono spese software per l'immagine di macchine Virtuali di scienze di dati. Pagando solo le commissioni utilizzo hardware Azure vengono valutate in base alla dimensione della macchina virtuale che viene effettuato il provisioning con l'immagine di macchina virtuale. Sono disponibili ulteriori informazioni sulle tariffe calcolo in [macchine Virtuali voce pagina Azure Marketplace ](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).


## <a name="prerequisites"></a>Prerequisiti

Prima di poter creare una macchina virtuale di Linux dati scienza, è necessario disporre le operazioni seguenti:

- **Azure un abbonamento**: per ottenere una, vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/free/).
- **Account di archiviazione di Azure**: per crearne uno, vedere [creare un account di archiviazione Azure](storage-create-storage-account.md#create-a-storage-account). In alternativa, è possibile creare l'account di archiviazione come parte del processo di creazione di macchine Virtuali, se non si desidera utilizzare un account esistente.


## <a name="create-your-linux-data-science-virtual-machine"></a>Creare la macchina virtuale di scienze Linux dati

Ecco la procedura per creare un'istanza di Linux dati scienza Virtual Machine:

1.  Passare alla macchina virtuale voce nel [portale di Azure](https://portal.azure.com/#create/microsoft-ads.linux-data-science-vmlinuxdsvm).
2.   Fare clic su **Crea** (nella parte inferiore) per visualizzare la procedura guidata. ![configurare-dati-scienza-macchine virtuali](./media/machine-learning-data-science-linux-dsvm-intro/configure-linux-data-science-virtual-machine.png)
3.   Le sezioni seguenti includono gli input per tutti i passaggi della procedura guidata (enumerato nella parte destra della figura precedente) utilizzati per creare Microsoft dati scienza Virtual Machine. Ecco gli input necessari per configurare queste operazioni:

    un. **Nozioni di base**:

  - **Nome**: nome del server di scienze dati che si sta creando.
  - **Nome utente**: primo account di accesso in ID.
  - **Password**: prima password dell'account (è possibile utilizzare la chiave pubblica SSH anziché password).
  - **Abbonamento**: se si hanno più abbonamenti, selezionare quella in cui il computer deve essere creato e fatturato. È necessario disporre dei privilegi di creazione delle risorse per l'abbonamento.
  - **Gruppo risorse**: È possibile crearne uno nuovo o utilizzare un gruppo esistente.
  - **Posizione**: selezionare il centro di dati più appropriato. In genere è il centro di dati con la maggior parte dei dati o più vicina alla posizione fisica per l'accesso rapido.

    b. **Dimensione**:

  - Selezionare uno dei tipi di server che soddisfi i requisiti funzionali e vincoli di costo. Selezionare **Visualizza tutto** per visualizzare altre opzioni di dimensioni macchine Virtuali.

    c. **Impostazioni**:

  - **Tipo di disco**: scegliere **Premium** se si preferisce un'unità di stato a tinta unita (SSD). In caso contrario, scegliere **Standard**.
  - **Account di archiviazione**: creare un nuovo account di archiviazione Azure nell'abbonamento o utilizzarne una esistente nella stessa posizione che è stato scelto nel passaggio **Nozioni di base** della procedura guidata.
  - **Gli altri parametri**: nella maggior parte dei casi è sufficiente utilizzare i valori predefiniti. Per valutare la possibilità di valori non predefiniti, passare il mouse sul collegamento informativo per altre informazioni sui campi specifici.

    d. **Riepilogo**:

  - Verificare che tutte le informazioni immesse siano corrette.

    e. **Acquistare**:

  - Per avviare il provisioning, fare clic su **Acquista**. Viene fornito un collegamento alle condizioni della transazione. La macchina virtuale non dispone di costi aggiuntivi oltre il calcolo per la dimensione del server che scelto nel passaggio **dimensioni** .

Il provisioning deve minuti circa 10-20. Nel portale di Azure viene visualizzato lo stato del provisioning.

## <a name="how-to-access-the-linux-data-science-virtual-machine"></a>Come accedere a Linux dati scienza Virtual Machine

Dopo aver creata la macchina virtuale, è possibile accedere a tale utilizzando SSH. Usare le credenziali dell'account creato nella sezione dei **concetti di base** del passaggio 3 per l'interfaccia di shell di testo. In Windows, è possibile scaricare uno strumento di client SSH come [Putty](http://www.putty.org). Se si preferisce un desktop con interfaccia grafico (sistema di Windows X), è possibile utilizzare X11 inoltro su Putty o installare il client X2Go.

>[AZURE.NOTE] Il client X2Go eseguite in modo significativo migliore X11 inoltro durante i test. È consigliabile utilizzare il client X2Go per un'interfaccia grafica desktop.


## <a name="installing-and-configuring-x2go-client"></a>Installazione e configurazione di client X2Go

VM Linux è già stato eseguito il provisioning con server X2Go e pronto per la connessione client. Per connettersi al desktop grafico Linux VM, eseguire le operazioni seguenti nel client:

1. Scaricare e installare il client X2Go per la piattaforma client [X2Go](http://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
2. Eseguire il client X2Go e selezionare **Nuova sessione**. Viene aperta una finestra di configurazione con più schede. Immettere i parametri di configurazione seguenti:
    * **Scheda sessione**:
        - **Host**: il nome host o l'indirizzo IP del supporto di scienze virtuale dati Linux.
        - **Accesso**: nome utente nella VM Linux.
        - **Porta SSH**: lasciare 22, il valore predefinito.
        - **Tipo di sessione**: modificare il valore in XFCE. VM Linux supporta attualmente solo desktop XFCE.
    * **Scheda clip multimediale**: È possibile disattivare supporto audio e client stampa se non è necessario usare quel gruppo.
    * **Cartelle condivise**: se si desidera directory dal computer client installato su VM Linux, aggiungere il cartelle del computer client che si desidera condividere con macchine Virtuali in questa scheda.

Dopo l'accesso per la macchina virtuale utilizzando il client SSH o desktop grafica XFCE tramite il client X2Go, si è pronti iniziare a usare gli strumenti che installati e configurati la macchina virtuale. Nella XFCE, è possibile visualizzare i collegamenti del menu applicazioni e le icone del desktop per molti degli strumenti.


## <a name="tools-installed-on-the-linux-data-science-virtual-machine"></a>Strumenti installati nel computer virtuale Linux dati scienze

### <a name="microsoft-r-open"></a>Apri Microsoft R
R è una delle lingue più comuni per l'analisi dei dati e apprendimento. Se si desidera utilizzare R per l'analitica, Microsoft R aperto (MRO) con matematiche Kernel raccolta (MKL) la macchina virtuale. Il MKL Ottimizza operazioni matematiche comuni algoritmi analitica. MRO è compatibile con CRAN-R al 100% e le raccolte R pubblicate in CRAN possono essere installate il MRO. È possibile modificare le applicazioni di R in uno degli editor predefiniti, ad esempio vi, Emacs o gedit. È anche possibile scaricare e usare altri IDE, ad esempio [RStudio](http://www.rstudio.com). Per comodità, viene fornito un semplice script (installRStudio.sh) nella directory **/dsvm/tools** installata RStudio. Se si utilizza l'editor Emacs, nota che la Emacs pacchetto ESS (Emacs Speaks statistiche), che semplifica l'utilizzo dei file R nell'editor Emacs è stato preinstallato.

Per avviare R, è sufficiente digitare **R** nella shell. Verrà visualizzato un ambiente interattivo. Per sviluppare il programma di R, in genere utilizzano un editor come Emacs o vi o gedit e quindi eseguire gli script all'interno di R. Se si installa RStudio, è necessario un ambiente IDE completo grafico sviluppare il programma di R.

È inoltre disponibile uno script R per l'installazione di [pacchetti superiore 20 R](http://www.kdnuggets.com/2015/06/top-20-r-packages.html) se si desidera. Dopo che ci si trova l'interfaccia interattiva R, è possibile immettere (come indicato) digitando **R** nella shell, è possibile eseguire questo script.  

### <a name="python"></a>Python
Per lo sviluppo tramite Python, distribuzione Anaconda Python 2.7 e 3.5 è stata installata. Questa distribuzione contiene Python base insieme a 300 circa dei pacchetti analitica matematiche, progettazione e i dati più popolari. È possibile utilizzare l'editor di testo predefinito. Inoltre, è possibile utilizzare Spyder, un IDE Python che viene fornito con distribuzioni Anaconda Python. Spyder necessita di un desktop con interfaccia grafico o X11 inoltro. Nella versione desktop di grafico è disponibile un collegamento a Spyder.

Poiché è stata creata, 2.7 Python e 3.5, è necessario attivare specificatamente la versione desiderata di Python che si desidera utilizzare nella sessione corrente. Il processo di attivazione imposta la variabile di percorso per la versione desiderata di Python.

Per attivare Python 2.7, eseguire le operazioni seguenti da shell:

    source /anaconda/bin/activate root

Python 2.7 viene installato */anaconda/bin*.

Per attivare Python 3.5, eseguire le operazioni seguenti da shell:

    source /anaconda/bin/activate py35


Python 3.5 viene installato */anaconda/envs/py35/bin*.

Per richiamare una sessione interattiva Python, è sufficiente digitare **python** nella shell. Se sono in un'interfaccia grafica o si X11 inoltro set di backup, è possibile digitare **spyder** per avviare l'IDE Python.

### <a name="jupyter-notebook"></a>Blocco appunti Jupyter

La distribuzione di Anaconda viene fornita con un blocco appunti Jupyter, un ambiente di condividere codice e analisi. Il blocco appunti Jupyter per accedere a JupyterHub. Accedi con il nome utente Linux locale e la password.

Il server di blocco appunti Jupyter è stato pre-configurato con Python 2, 3 Python e vengono R. Non c'è un'icona sul desktop denominata "Blocco appunti Jupyter" per aprire il browser per accedere al server di blocco appunti. Se utilizza la macchina virtuale tramite client SSH o X2Go, è inoltre possibile visitare [https://localhost:8000 /](https://localhost:8000/) per accedere al server di blocco appunti Jupyter.

>[AZURE.NOTE] Continuare se viene visualizzato tutti gli avvisi certificato.

È possibile accedere al server di blocco appunti Jupyter da qualsiasi host. È sufficiente digitare *https://\<VM DNS nome o indirizzo IP\>: 8000 /*

>[AZURE.NOTE] Porta 8000 viene aperta nel firewall per impostazione predefinita quando viene eseguito il provisioning macchina virtuale.

È stato fornito esempi di blocchi appunti, ossia Python in una sola e altro R. È possibile visualizzare il collegamento per gli esempi nella home page di blocco appunti dopo autenticazione al blocco appunti Jupyter utilizzando il nome utente Linux locale e la password. È possibile creare un nuovo blocco appunti, selezionare **Nuovo**e quindi kernel lingua appropriata. Se il pulsante **Nuovo** non è visualizzato, fare clic sull'icona **Jupyter** in alto a sinistra per passare alla home page del server di blocco appunti.


### <a name="ides-and-editors"></a>IDE ed editor

È possibile scegliere di diversi editor di codice. Sono inclusi vi/VIM, Emacs, gEdit ed Eclisse. gEdit Eclisse sono editor grafico ed è necessario avere eseguito l'accesso a un desktop con interfaccia grafico usarle. Questi editor dispongono di desktop e applicazioni i collegamenti del menu per l'avvio.

**VIM** ed **Emacs** sono editor basata su testo. In Emacs, è stato installato un pacchetto di componente aggiuntivo chiamato Emacs Speaks statistiche (ESS) che facilita all'interno dell'editor Emacs di lavoro con R. Sono disponibili ulteriori informazioni su [ESS](http://ess.r-project.org/).

**Eclisse** è un'origine aperta, extensible IDE che supporta più lingue. L'edizione gli sviluppatori di linguaggio è l'istanza installata nella macchina virtuale. Sono disponibili per diverse lingue comuni che possono essere installate per estendere l'ambiente Eclisse plug-in. Abbiamo anche un plug-in installati in Eclisse denominato **Toolkit di Azure per Eclisse**. Consente di creare, sviluppare, testare e distribuire le applicazioni Azure che usano l'ambiente di sviluppo Eclipse che supporta lingue come linguaggio. È inoltre disponibile un **Azure SDK per Java** che consente l'accesso a diversi servizi di Azure da un ambiente di linguaggio. Sono disponibili ulteriori informazioni su Azure toolkit per Eclisse in [Azure Toolkit per Eclisse](../azure-toolkit-for-eclipse.md).

**Acrilica** viene installato tramite il pacchetto di texlive insieme package [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) aggiuntivo Emacs, che consente di semplificare la creazione di documenti acrilica interno Emacs.  

### <a name="databases"></a>Database

#### <a name="postgres"></a>Postgres
Il database aperto **Postgres** è disponibile in macchine Virtuali, con i servizi in esecuzione e initdb già completato. È comunque necessario creare database e utenti. Per ulteriori informazioni, vedere la [documentazione Postgres](https://www.postgresql.org/docs/).  

####  <a name="graphical-sql-client"></a>Grafica SQL client
**Esce SQL**, un client SQL grafico, sono state ricevute le autorizzazioni per connettersi al database diversi (ad esempio Microsoft SQL Server, Postgres e MySQL) ed eseguire query SQL. È possibile eseguire da una sessione desktop grafica (utilizzando il client X2Go, ad esempio). Per richiamare esce SQL, è possibile avviare dall'icona sul desktop o eseguire il comando seguente su shell.

    /usr/local/squirrel-sql-3.7/squirrel-sql.sh

Al primo utilizzo, impostare il driver e gli alias di database. Driver JDBC si trovano in:

*/usr/share/Java/jdbcdrivers*

Per ulteriori informazioni, vedere [Esce SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

#### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Strumenti della riga di comando per l'accesso a Microsoft SQL Server

Il pacchetto di driver ODBC per SQL Server è disponibili due strumenti della riga di comando:

**bcp**: in blocco utilità bcp copia dei dati tra un'istanza di Microsoft SQL Server e un file di dati in un formato specificato dall'utente. Per importare un numero elevato di nuove righe in tabelle di SQL Server o esportare i dati dalle tabelle nei file di dati, è possibile utilizzare l'utilità bcp. Per importare dati in una tabella, è necessario usare un file in formato creato per la tabella o comprendere la struttura della tabella e i tipi di dati validi per le colonne.

Per ulteriori informazioni, vedere [connessione con bcp](https://msdn.microsoft.com/library/hh568446.aspx).

**SQLCMD**: È possibile immettere istruzioni Transact-SQL con utilità sqlcmd, nonché le procedure di sistema e file al prompt dei comandi di script. Questa utilità ODBC viene utilizzato per eseguire batch Transact-SQL.

Per ulteriori informazioni, vedere [connessione con sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

>[AZURE.NOTE] Esistono alcune differenze tra questa utilità tra piattaforme Linux e Windows. Vedere la documentazione per informazioni dettagliate.


#### <a name="database-access-libraries"></a>Librerie di accesso al database

Sono disponibili le raccolte in R e Python ai database di access.

- In R, il **RODBC** o **dplyr** pacchetto consente di eseguire una query o eseguire istruzioni SQL nel server di database.
- In Python, la libreria **pyodbc** offre accesso al database con ODBC come livello sottostante.  

Per accedere a **Postgres**:

- Da r: Usare il pacchetto **RPostgreSQL**.
- Da Python: Utilizzare la libreria **psycopg2** .


### <a name="azure-tools"></a>Strumenti di Azure
Gli strumenti di Azure seguenti sono installati nella macchina virtuale:

- **Interfaccia di riga di comando Azure**: la CLI Azure consente di creare e gestire Azure mediante i comandi di shell. Per richiamare gli strumenti di Azure, è sufficiente digitare **Guida azure**. Per ulteriori informazioni, vedere la [pagina della documentazione di Azure CLI](../virtual-machines-command-line-tools.md).
- **Esplora archivi di Microsoft Azure**: Microsoft Azure archiviazione Explorer è uno strumento grafico utilizzato per spostarsi tra i vari oggetti archiviato nel proprio account di archiviazione Azure e per caricare e scaricare i dati da e verso BLOB Azure. È possibile accedere Esplora archivi dall'icona di collegamento sul desktop. È possibile richiamare da un prompt di shell digitando **StorageExplorer**. È necessario che la firma da un client X2Go o hanno X11 inoltro set di backup.
- **Raccolte di Azure**: di seguito sono alcune delle raccolte preinstallate.

 - **Python**: correlati di Azure raccolte in Python che vengono installate sono **azure**, **azureml**, **pydocumentdb**e **pyodbc**. Con le prime tre raccolte, è possibile accedere servizi di archiviazione Azure, apprendimento Azure e Azure DocumentDB (un database NoSQL Azure). La raccolta quarto pyodbc (insieme a Microsoft driver ODBC per SQL Server), consente di accedere a SQL Server, Database SQL Azure e Azure SQL Data Warehouse dal Python mediante un'interfaccia ODBC. Immettere **elenco pip** per visualizzare tutte le raccolte nell'elenco. Assicurarsi di eseguire il comando 3,5 ambienti e 2.7 Python.
 - **R**: librerie di Azure correlati in R che vengono installate sono **AzureML** e **RODBC**.
 - **Linguaggio**: l'elenco di raccolte di Azure Java sono disponibili nella directory più **/dsvm/sdk/AzureSDKJava** nella macchina virtuale. Raccolte chiave sono Azure archiviazione e la gestione API, DocumentDB e JDBC driver di SQL Server.  

È possibile accedere al [portale di Azure](https://portal.azure.com) dal browser Firefox preinstallati. Nel portale di Azure, è possibile creare, gestire e monitorare le risorse di Azure.

### <a name="azure-machine-learning"></a>Apprendimento Azure

Apprendimento Azure è un servizio cloud completamente gestita che consente di compilare, distribuire e condividere soluzioni analitica previsione. Compilare l'esperimenti e i modelli da Azure Machine Learning Studio. Possibile accedervi da un web browser sul computer virtuale scienza dati visitando il sito [Microsoft Azure apprendimento](https://studio.azureml.net).

Dopo l'accesso a Azure Machine formazione Studio, è possibile accedere a un'area di sperimentazione che consente di creare un flusso logico per l'apprendimento automatico degli algoritmi. Inoltre, hanno accesso a un blocco appunti Jupyter ospitato in Azure apprendimento e possono lavorare facilmente con esperimenti Machine Learning Studio. Rendono operativi i modelli compilati dalla inserirli in un'interfaccia servizi web di apprendimento. In questo modo client scritti in qualsiasi lingua richiamare le stime dal computer di modelli di apprendimento. Per ulteriori informazioni, vedere la [documentazione per l'apprendimento](https://azure.microsoft.com/documentation/services/machine-learning/).

È possibile anche creare i modelli in R o Python nella macchina virtuale e quindi distribuirlo in produzione apprendimento Azure. È stato installato raccolte in R (**AzureML**) e Python (**azureml**) per abilitare questa funzionalità.

Per informazioni su come distribuire modelli in R e Python in apprendimento Azure, vedere [dieci operazioni da eseguire sulla ricerca di scienze dati macchina virtuale](machine-learning-data-science-vm-do-ten-things.md) (in particolare, la sezione "creare modelli utilizzando R o Python e li rendono operativi con Azure apprendimento").

>[AZURE.NOTE] Queste istruzioni sono stati scritti per la versione di Windows di macchine Virtuali di scienze dati. Ma disponibili le informazioni sulla distribuzione di modelli per l'apprendimento Azure è applicabile per VM Linux.

### <a name="machine-learning-tools"></a>Strumenti di apprendimento computer

La macchina virtuale viene fornito con alcuni computer algoritmi pre-compilati e preinstallati in locale e gli strumenti di apprendimento. Sono inclusi:

* **CNTK** (Calcolo rete Toolkit da Microsoft Research): una profondità toolkit di apprendimento.
* **Vowpal Wabbit**: un algoritmo di apprendimento rapidamente online.
* **xgboost**: uno strumento che fornisce algoritmi albero invece un incremento e ottimizzato.
* **Python**: Anaconda Python fornito in dotazione con algoritmi risorse computer con le raccolte come scoprire Scikit. È possibile installare altre raccolte tramite il `pip install` comando.
* **R**: una libreria completa di funzioni di apprendimento computer è disponibile per R. Alcune delle raccolte che sono preinstallate sono lm, glm, randomForest, rpart. Altre raccolte possono essere installati eseguendo:

        install.packages(<lib name>)

Ecco alcune informazioni aggiuntive sugli strumenti di tre apprendimento prima nell'elenco.

#### <a name="cntk"></a>CNTK
Si tratta di un'origine aperta, completa toolkit di apprendimento. È uno strumento della riga di comando (cntk) e già presente nel percorso.

Per eseguire un campione di base, eseguire i comandi seguenti nella shell:

    # Copy samples to your home directory and execute cntk
    cp -r /dsvm/tools/CNTK-2016-02-08-Linux-64bit-CPU-Only/Examples/Other/Simple2d cntkdemo
    cd cntkdemo/Data
    cntk configFile=../Config/Simple.cntk

Il modello output è *~/cntkdemo/Output/Models*.

Per ulteriori informazioni, vedere la sezione CNTK di [GitHub](https://github.com/Microsoft/CNTK)e [CNTK wiki](https://github.com/Microsoft/CNTK/wiki).


#### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit è un sistema che utilizza tecniche, ad esempio online, hashing, allreduce, riduzioni, learning2search, attivo, di apprendimento e apprendimento interattivo.

Per eseguire lo strumento su un esempio molto semplice, eseguire le operazioni seguenti:

    cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
    cd vwdemo
    vw house_dataset

Sono disponibili altri di grandi dimensioni demo nella directory. Per ulteriori informazioni sul VW, vedere [questa sezione della GitHub](https://github.com/JohnLangford/vowpal_wabbit)e [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki).

#### <a name="xgboost"></a>xgboost
Si tratta di una raccolta che progettata e ottimizzata per algoritmi invece un incremento (struttura). L'obiettivo di questa raccolta è per inserire i limiti di calcolo del computer a estremi necessari per fornire albero su larga scala incrementare che scalable, portatile e accurato.

Viene fornito come una riga di comando, nonché una libreria R.

Per utilizzare questa raccolta R, è possibile avviare una sessione di R interattiva (digitando semplicemente **R** nella shell) e caricare la libreria.

Ecco un semplice esempio che è possibile eseguire al prompt dei comandi R:

    library(xgboost)

    data(agaricus.train, package='xgboost')
    data(agaricus.test, package='xgboost')
    train <- agaricus.train
    test <- agaricus.test
    bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                    eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
    pred <- predict(bst, test$data)

Per eseguire la riga di comando xgboost, ecco i comandi da eseguire nella shell:

    cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
    cd xgboostdemo
    xgboost mushroom.conf


Un file. Model è scritto nella directory specificata. Sono disponibili informazioni su questo esempio demo [su GitHub](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Per ulteriori informazioni su xgboost, vedere la [pagina della documentazione xgboost](https://xgboost.readthedocs.org/en/latest/)e il relativo [archivio Github](https://github.com/dmlc/xgboost).

#### <a name="rattle"></a>Sonagli
Sonaglio ( **R** **A**nalytical **T**Tool esegue **T**o **L**ottenere **E**asily) utilizza modellazione e l'esplorazione di dati basate su interfaccia grafica. Presenta statistici e visivi riepiloghi dei dati, le trasformazioni che possono essere facilmente basato, consente di creare modelli senza supervisione e controllati dai dati, vengono presentate le prestazioni dei modelli graficamente, e set di nuovi dati punteggi. Viene inoltre generato codice R, replica le operazioni nell'interfaccia utente che possono essere eseguite direttamente in R o utilizzate come punto di partenza per un'ulteriore analisi.

Per eseguire sonagli, è necessario essere in una grafica accesso sessione desktop. In terminale, digitare ```R``` per accedere all'ambiente di R. Al prompt dei comandi R, immettere i comandi seguenti:

    library(rattle)
    rattle()

Un'interfaccia grafica aperta con una serie di schede. Ecco la procedura di Guida introduttiva di sonagli necessarie per utilizzare un set di dati di esempio meteo e creazione di un modello. In alcune delle procedure seguenti, viene chiesto di installare e caricare alcuni pacchetti R necessari che non sono presenti nel sistema automaticamente.

>[AZURE.NOTE] Se non si dispone di accesso per installare il pacchetto nella directory di sistema (impostazione predefinita), viene visualizzato un prompt dei comandi nella finestra console R per installare i pacchetti alla raccolta personale. Rispondere *y* se viene visualizzato queste istruzioni.

1. Fare clic su **Esegui**.
2. Una finestra di dialogo appare, in cui viene richiesto se si desidera utilizzare il set di dati di esempio meteo. Fare clic su **Sì** per caricare l'esempio.
3. Fare clic sulla scheda **modello** .
4. Fare clic su **Esegui** per creare un albero decisionale.
5. Fare clic su **Disegna** per visualizzare l'albero decisionale.
6. Fare clic sul pulsante di opzione **foresta** e fare clic su **Esegui** per creare un insieme di strutture casuali.
7. Fare clic sulla scheda **valuta** .
8. Fare clic sul pulsante di opzione **i rischi** e fare clic su **Esegui** per visualizzare due posti le prestazioni di rischio (cumulativo).
9. Fare clic sulla scheda **Log** per visualizzare il codice genera R per le operazioni precedenti.
(A causa di un bug nella versione corrente di sonagli, è necessario inserire un *#* carattere che precede *esportare il registro …* nel testo del registro.)
10. Fare clic sul pulsante **Esporta** per salvare il file di script R denominato *weather_script. R* alla cartella principale.

È possibile uscire sonagli e R. A questo punto modificare lo script R generato oppure utilizzarla come verrà eseguito in qualsiasi momento per ripetere tutto ciò che venisse eseguito all'interno dell'interfaccia Rattle. In particolare per principianti in R, questo è un modo semplice per eseguire analisi e apprendimento in un'interfaccia grafica semplice, durante la generazione automatica di codice in R per modificare e/o il computer rapidamente.


## <a name="next-steps"></a>Passaggi successivi
Ecco come è possibile continuare l'esplorazione e risorse:

* Procedura dettagliata di [scienze di dati in Linux dati scienza Virtual Machine](machine-learning-data-science-linux-dsvm-walkthrough.md) viene illustrato come eseguire diverse attività comuni di scienze dati con la macchina virtuale scienza di dati Linux viene completato il provisioning di seguito. 
* Esplorare i vari strumenti di ricerca di scienze di dati nella ricerca di scienze dati macchine Virtuali provato a utilizzare gli strumenti descritti in questo articolo. È possibile eseguire anche *informazioni di altre dsvm* nella shell all'interno della macchina virtuale per un'introduzione e puntatori a ulteriori informazioni sugli strumenti installati nella macchina virtuale.  
* Informazioni su come creare soluzioni analitiche-to-end sistematica utilizzando il [Processo di scienze dei dati del Team](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).
* Visitare la [Raccolta Analitica Cortana](http://gallery.cortanaanalytics.com) per computer apprendimento e dati analitica esempi che utilizzano la famiglia Analitica Cortana.
