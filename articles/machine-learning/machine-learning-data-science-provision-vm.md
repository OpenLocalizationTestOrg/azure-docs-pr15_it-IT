<properties 
    pageTitle="Effettuare il provisioning di Microsoft Data scienza Virtual Machine | Microsoft Azure" 
    description="Configurare e creare una macchina virtuale di scienze dati su Azure procedura analitica e lavorare risorse." 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/07/2016" 
    ms.author="bradsev" />


# <a name="provision-the-microsoft-data-science-virtual-machine"></a>Effettuare il provisioning di Microsoft Data scienza Virtual Machine

Microsoft dati scienza Virtual Machine è un'immagine di Azure macchine () già installato e configurato con più comuni strumenti di uso comune per analitica dati e apprendimento. Gli strumenti disponibili sono:

- Microsoft R Server Developer Edition
- Distribuzione di Python anaconda
- Blocco appunti Jupyter (con R, vengono Python)
- Versione della Community di Visual Studio
- Desktop di Power BI
- SQL Server 2016 Developer Edition
- Strumenti di apprendimento computer
    - [Calcolo rete Toolkit (CNTK)](https://github.com/Microsoft/CNTK): una profondità toolkit di software da Microsoft Research di apprendimento.
    - [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): un computer veloce formazione system che supporta le tecniche, ad esempio online, hashing, allreduce, riduzioni, learning2search, attivo e apprendimento interattivo.
    - [XGBoost](https://xgboost.readthedocs.org/en/latest/): uno strumento che fornisce l'implementazione di albero invece un incremento veloce e accurato.
    - [Rattle](http://rattle.togaware.com/) (la R Analytical strumento per informazioni su facilmente): uno strumento che consente di introduzione a analitica dati e computer R semplice con esplorazione dei dati basati sull'interfaccia di apprendimento e modellazione con generazione automatica di codice R.
    - [mxnet](https://github.com/dmlc/mxnet): un quadro di apprendimento complete progettato per l'efficienza e flessibilità
- Raccolte in R e Python per usare in Azure apprendimento e altri servizi di Azure
- Operazioni compresi fra Bash per l'uso con archivi di codice sorgente tra cui GitHub, Visual Studio Team Services
- Porte di Windows di più popolari Linux utilità riga di comando (inclusi awk, sed, perl, grep, trovare, wget, curvatura e così via) accessibili tramite il prompt dei comandi. 


Eseguire una ricerca di scienze dati a altro comporta l'iterazione su una sequenza delle attività: dati di ricerca, il caricamento e pre-elaborazione, la creazione e verifica dei modelli e distribuzione i modelli per consumo nelle applicazioni intelligente. Scienziati dati utilizzano una varietà di strumenti per eseguire queste attività. Può essere molto tempo per trovare le versioni appropriate del software e quindi scaricare e installarli. Microsoft dati scienza Virtual Machine ovviare a questo carico fornendo un'immagine pronte per l'uso che è possibile eseguire il provisioning su Azure con tutte le più comuni strumenti già installato e configurato. 

Microsoft dati scienza Virtual Machine jump-starts progetto analitica. Consente di allo svolgimento di attività in diverse lingue inclusi R, Python, SQL e c#. Visual Studio fornisce un IDE per sviluppare e testare il codice è facile da usare. Azure SDK incluso nella macchina virtuale consente di creare le applicazioni usando vari servizi nel cloud la piattaforma Microsoft. 

Non esistono spese software per l'immagine di macchine Virtuali di scienze di dati. Pagando solo per i diritti Azure che dipendono le dimensioni della macchina virtuale che viene effettuato il provisioning. Ulteriori informazioni sulle tariffe calcolo disponibili nella sezione dettagli prezzi nella pagina [dati scienza Virtual Machine](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) . 


## <a name="prerequisites"></a>Prerequisiti

Prima di poter creare una macchina virtuale di Microsoft dati scienza, è necessario disporre le operazioni seguenti:

- **Azure un abbonamento**: per ottenere una, vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

*   **Account di archiviazione di Azure**: per crearne uno, vedere [creare un account di archiviazione Azure](storage-create-storage-account.md#create-a-storage-account). In alternativa, è possibile creare l'account di archiviazione come parte del processo di creazione della macchina virtuale se non si desidera utilizzare un account esistente.


## <a name="create-your-microsoft-data-science-virtual-machine"></a>Creare la macchina virtuale di scienze dati Microsoft

Ecco la procedura per creare un'istanza di Microsoft dati scienza Virtual Machine:

1.  Passare alla macchina virtuale voce nel [portale Azure](https://portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm).
2.   Selezionare il pulsante **Crea** nella parte inferiore da prendere in una procedura guidata. ![configurare-dati-scienza-macchine virtuali](./media/machine-learning-data-science-provision-vm/configure-data-science-virtual-machine.png)
3.   La procedura guidata utilizzata per creare Microsoft dati scienza Virtual Machine richiede **input** per tutti i **cinque passaggi** enumerati nella parte destra della figura. Ecco gli input necessari per configurare queste operazioni:
    
    1.   **Nozioni di base**
        1.   **Nome**: nome del server di scienze dati che si sta creando.
        2.   **Nome utente**: id di accesso di account di amministratore.
        3.   **Password**: password dell'account di amministratore.
        4.   **Abbonamento**: se si hanno più abbonamenti, selezionare quella in cui il computer deve essere creato e fatturato.
        5.   **Gruppo risorse**: È possibile crearne uno nuovo o utilizzare un gruppo esistente.
        6.   **Posizione**: selezionare il centro di dati più appropriato. In genere è il centro di dati che include la maggior parte dei dati o più vicina alla posizione fisica per l'accesso rapido.
         
    2.   **Dimensione**: selezionare uno dei tipi di server che soddisfi i requisiti funzionali e vincoli di costo. È possibile ottenere altre opzioni di dimensioni macchine Virtuali selezionando "View All".
    
    3.   **Impostazioni**:
        1.   **Tipo di disco**: scegliere Premium se si preferisce un'unità (SSD), scegliere altro "Standard".
        2.   **Account di archiviazione**: È possibile creare un nuovo account di archiviazione Azure nell'abbonamento o utilizzarne una esistente nella stessa *posizione* in cui è stato scelto nel passaggio **Nozioni di base** della procedura guidata.
        3.   **Gli altri parametri**: in genere è sufficiente utilizzare i valori predefiniti. È possibile posizionare il puntatore sul collegamento informativo per altre informazioni sui campi specifici nel caso in cui si desidera considerare l'utilizzo di valori non predefiniti.

    4.   **Riepilogo**: verificare che tutte le informazioni immesse siano corrette.
    5.   **Acquistare**: fare clic su **Acquista** per avviare il provisioning. Viene fornito un collegamento alle condizioni della transazione. La macchina virtuale non dispone di costi aggiuntivi oltre il calcolo per la dimensione del server che scelto nel passaggio **dimensioni** . 


>[AZURE.NOTE] Il provisioning deve minuti circa 10-20. Nel portale di Azure viene visualizzato lo stato del provisioning.

## <a name="how-to-access-the-microsoft-data-science-virtual-machine"></a>Come accedere a Microsoft dati scienza Virtual Machine

Dopo aver creata la macchina virtuale, è possibile desktop remoto dentro usando le credenziali dell'account di amministratore che è stato configurato nella sezione **informazioni di base sul** precedente. 

Una volta creata e viene completato il provisioning di una macchina virtuale, si è pronti iniziare a usare gli strumenti che installati e configurati su di esso. Sono disponibili i riquadri di menu start e le icone del desktop per molti degli strumenti. 

## <a name="how-to-create-a-strong-password-on-the-jupyter-notebook-server"></a>Come creare una password complessa sul server Jupyter blocco appunti 

Per creare la propria password complesse per il server di blocco appunti Jupyter installato nel computer, eseguire il seguente comando da un prompt dei comandi in dati scienza Virtual Machine.

    c:\anaconda\python.exe -c "import IPython;print IPython.lib.passwd()"

Scegliere una password complessa quando richiesto.

Viene visualizzato l'hash della password in formato "sha1:xxxxxx" nell'output. Copiare questa hash password e sostituire l'hash esistente nel file di configurazione del blocco appunti che si trova in: **C:\ProgramData\jupyter\jupyter_notebook_config.py** con un nome parametro ***c.NotebookApp.password***.

Sostituire solo la parte (xxxxxx) del valore hash esistenti tra virgolette. Le virgolette e il ***sha1:*** prefisso per il valore del parametro entrambe occorre conservare.

Infine, è necessario arrestare e riavviare il server Jupyter, che è in esecuzione nella macchina virtuale come un'attività programmata windows chiamata **Start_IPython_Notebook**. Se la nuova password non viene accettata dopo il riavvio di questa operazione, provare a eliminare tutti i processi di python in esecuzione da Gestione attività e quindi riavviare l'attività pianificata. In alternativa, provare a riavviare la macchina virtuale.

## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Strumenti installati in Microsoft dati scienza Virtual Machine

### <a name="microsoft-r-server-developer-edition"></a>Microsoft R Server Developer Edition
Se si desidera utilizzare R per l'analitica, Microsoft R Server Developer edition installato la macchina virtuale. Microsoft R Server è una piattaforma analitica aziendale ampiamente distribuita in base alle R supportato, scalable e sicura. Diverse statistiche di dati, modellazione previsione e apprendimento automatico funzionalità di supporto, R Server in uso supporta la gamma completa di analitica-esplorazione, analisi, visualizzazione e modellazione. Utilizzando e si estende Apri origine R, Microsoft R Server è compatibile con script R, funzioni e i pacchetti CRAN, per analizzare i dati in scala aziendale. Inoltre, gli indirizzi limitazioni in memoria di Apri origine R mediante l'aggiunta di elaborazione in parallelo e in blocchi di dati. In questo modo è possibile eseguire analitica sui dati molto superiori a quali appartiene memoria principale.  Visual Studio Community Edition incluso nella macchina virtuale contiene gli strumenti di R per estensione di Visual Studio che fornisce un IDE completo per l'utilizzo di R. È anche possibile scaricare e usare altri IDE anche, ad esempio [RStudio](http://www.rstudio.com). 

### <a name="python"></a>Python
Per lo sviluppo tramite Python, distribuzione Anaconda Python 2.7 e 3.5 è stata installata. Questa distribuzione contiene Python base insieme a 300 circa dei pacchetti analitica matematiche, progettazione e i dati più popolari. È possibile utilizzare gli strumenti di Python per Visual Studio (PTVS) che è installato all'interno dell'edizione di Visual Studio 2015 Community o uno dei IDE in dotazione con Anaconda come inattivo o Spyder. È possibile avviare uno di questi piani ricerca sulla barra di ricerca (**vincono** + tasto**S** ).

>[AZURE.NOTE] Per scegliere gli strumenti di Python per Visual Studio Anaconda Python 2.7 e 3.5, è necessario creare ambienti personalizzati per ogni versione. Per impostare questi percorsi ambiente Visual Studio 2015 Community Edition, passare a **Strumenti** -> **Python strumenti** -> **Python ambienti** e quindi fare clic su **+ personalizzata**. 

Anaconda Python 2.7 viene installato in C:\Anaconda e Anaconda Python 3.5 è installato in c:\Anaconda\envs\py35. Per istruzioni dettagliate, vedere [la documentazione PTVS](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it) . 

### <a name="jupyter-notebook"></a>Blocco appunti Jupyter
Distribuzione anaconda viene fornita con un blocco appunti Jupyter, un ambiente di condividere codice e analisi. Un server di blocco appunti Jupyter è stato pre-configurato con Python 2, 3 Python e vengono R. Esiste un'icona sul desktop denominata "blocco appunti Jupyter per aprire il browser per accedere al server di blocco appunti. Se utilizza la macchina virtuale tramite desktop remoto, è inoltre possibile visitare [https://localhost:9999 /](https://localhost:9999/) per accedere al server di blocco appunti Jupyter quando è connesso al macchina virtuale.
 
>[AZURE.NOTE] Continuare se viene visualizzato tutti gli avvisi certificato. 

È stato fornito esempi di blocchi appunti in Python e R. I blocchi appunti Jupyter viene illustrato come utilizzare Microsoft R Server, SQL Server 2016 R Services (analitica nel database), Python e altre tecnologie di Azure dopo l'accesso a Jupyter. Dopo l'autenticazione per il blocco appunti Jupyter utilizzando la password creata in precedenza, è possibile visualizzare il collegamento per gli esempi nella home page di blocco appunti. 

### <a name="visual-studio-2015-community-edition"></a>Visual Studio 2015 Community edition
Visual Studio Community edition installato nella macchina virtuale. Si tratta di una versione gratuita di IDE più diffuso da Microsoft che è possibile utilizzare per la valutazione e per piccoli team. È possibile estrarre le condizioni di licenza [di seguito](https://www.visualstudio.com/support/legal/mt171547).  Aprire Visual Studio facendo doppio clic sull'icona del desktop o il menu **Start** . È inoltre possibile cercare programmi con **vincono** + **S** e se si immette "Visual Studio". Una volta sono è possibile creare progetti in lingue come c#, Python, R, Node. Plug-in vengono installati anche che ne semplificano l'uso con Azure servizi come catalogo dati di Azure, Azure HDInsight (Hadoop, motori) e Lake di dati di Azure. 

>[AZURE.NOTE] Potrebbe essere visualizzato un messaggio che informa che il periodo di valutazione è scaduto. Immettere le credenziali dell'account Microsoft o creare un nuovo account gratuito per accedere a Visual Studio Community Edition. 

### <a name="sql-server-2016-developer-edition"></a>SQL Server 2016 Developer edition
Si è disponibile una versione di sviluppo di SQL Server 2016 con i servizi di R eseguire nel database analitica nella macchina virtuale. R servizi forniscono una piattaforma per lo sviluppo e distribuzione di applicazioni intelligente. Per creare modelli e generare le stime per i dati di SQL Server, è possibile usare la lingua di R ricca e potente e molti pacchetti dalla community. È possibile mantenere analitica vicina ai dati poiché R integrazione dei servizi (nel database) lingua R con SQL Server. Per evitare i costi e i rischi associati allo spostamento di dati.

>[AZURE.NOTE] L'edizione di sviluppo di SQL Server 2016 può essere usata solo per scopi di sviluppo e test. È necessaria una licenza per l'esecuzione di produzione. 

È possibile accedere a SQL server avviando **SQL Server Management Studio**. Il nome di macchine Virtuali viene inserito come il nome del Server. Utilizzare l'autenticazione di Windows durante l'accesso come amministratore in Windows. Una volta in SQL Server Management Studio è possibile creare altri utenti, creare i database, importare i dati ed eseguire query SQL. 

Per abilitare analitica nel database utilizzando Microsoft R, eseguire il comando seguente come definire l'azione SQL Server management Studio dopo l'accesso come amministratore del server. 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 
        
        (Please replace the %COMPUTERNAME% with your VM name)


### <a name="azure"></a>Azure 
Diversi strumenti Azure vengono installati nella macchina virtuale:

- Esiste un collegamento sul desktop per accedere alla documentazione di Azure SDK. 
- **AzCopy**: utilizzato per spostare i dati da e verso l'Account di Microsoft Azure lo spazio di archiviazione. Per visualizzare l'uso, digitare **Azcopy** al prompt dei comandi per visualizzare l'utilizzo. 
- **Esplora archivi di Microsoft Azure**: utilizzato per spostarsi tra gli oggetti memorizzati all'interno dei dati di Account di archiviazione di Azure e trasferimento da e verso lo spazio di archiviazione Azure. È possibile immettere **Lo spazio di archiviazione Explorer** ricerca o trovare dal menu Start di Windows per accedere a questo strumento. 
- **Adlcopy**: utilizzato per spostare i dati di Azure dati Lake. Per visualizzare l'uso, digitare **adlcopy** in un prompt dei comandi. 
- **dtui**: utilizzato per spostare i dati da e verso DocumentDB Azure, un database NoSQL nel cloud. Digitare **dtui** su prompt dei comandi. 
- **Gateway di gestione dati Microsoft**: consente di spostamento dei dati tra origini dati locali e cloud. Viene utilizzato in strumenti come Azure Data Factory. 
- **Microsoft Azure Powershell**: strumento che consente di amministrare le risorse Azure PowerShell linguaggio di script è installato anche nella macchina virtuale. 

###<a name="power-bi"></a>Power BI

Per informazioni su come creare dashboard e visualizzazioni ottimali, è stato installato **Power BI Desktop** . Utilizzare questo strumento per estrarre i dati da origini diverse, per modificare i report e dashboard e pubblicarli nel cloud. Per informazioni, vedere il sito di [Power BI](http://powerbi.microsoft.com) . È possibile trovare Power BI desktop nel menu Start. 

>[AZURE.NOTE] È necessario un account Office 365 per accedere a Power BI. 

## <a name="additional-microsoft-development-tools"></a>Altri strumenti di sviluppo di Microsoft
[**Installazione guidata piattaforma Web Microsoft**](https://www.microsoft.com/web/downloads/platform.aspx) può essere utilizzato per individuare e scaricare altri strumenti di sviluppo Microsoft. È inoltre disponibile un collegamento per lo strumento fornito sul desktop Microsoft dati scienza Virtual Machine.  

## <a name="important-directories-on-the-vm"></a>Directory importanti nella macchina virtuale

| Elemento                          | Directory |
| ------------------------------| ---------------- |
|Configurazioni server blocco appunti Jupyter | C:\ProgramData\jupyter |
|Directory iniziale di blocco appunti Jupyter esempi| c:\dsvm\notebooks|
|Altri esempi | c:\dsvm\samples|
| Anaconda (impostazione predefinita: Python 2.7) | c:\Anaconda |
| Ambiente Python 3.5 anaconda | c:\Anaconda\envs\py35|
|Directory dell'istanza autonoma di Server R (istanza predefinita R) | C:\Programmi\Microsoft c:\Programmi\Microsoft SQL Server\130\R_SERVER |
| Directory dell'istanza R Server di database | C:\Programmi\Microsoft c:\Programmi\Microsoft SQL Server\MSSQL13. MSSQLSERVER\R_SERVICES |
| Vari strumenti | c:\dsvm\tools|

>[AZURE.NOTE] Le istanze di Microsoft dati scienza Virtual Machine creati prima 1.5.0 (prima 3 settembre 2016) utilizzate una struttura di directory leggermente diverso da quello specificato nella tabella precedente. 

## <a name="next-steps"></a>Passaggi successivi
Ecco alcuni passaggi successivi per continuare l'esplorazione e risorse. 

* Esplorare i vari strumenti di ricerca di scienze dati sulla ricerca di scienze dati macchine Virtuali facendo clic sul pulsante Start e l'estrazione gli strumenti elencati nel menu.
* Passare a **C:\Programmi\Microsoft c:\Programmi\Microsoft SQL Server\130\R_SERVER\library\RevoScaleR\demoScripts** per esempi di utilizzo della libreria di RevoScaleR R che supporta analitica di dati in scala aziendale.  
* Leggere l'articolo: [10 operazioni da eseguire sulla ricerca di scienze dati macchina virtuale](http://aka.ms/dsvmtenthings)
* Informazioni su come creare soluzioni analitiche to end sistematica utilizzando il [Processo di scienze dei dati del Team](https://azure.microsoft.com/documentation/learning-paths/data-science-process/).
* Visitare la [Raccolta di Business Intelligence Cortana](http://gallery.cortanaintelligence.com) per computer apprendimento e dati analitica esempi che utilizzano la famiglia di Business Intelligence Cortana. Sono inoltre disponibili un'icona sul **pulsante Start** e sul desktop del computer virtuale per questa raccolta.

