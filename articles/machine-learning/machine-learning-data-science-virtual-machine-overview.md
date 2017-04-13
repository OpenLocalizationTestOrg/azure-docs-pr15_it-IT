<properties
    pageTitle="Che cos'è una macchina virtuale di scienze dati? | Microsoft Azure"
    description="Informazioni su scenari principali caratteristiche e come iniziare con dati scienza macchine virtuali, un ambiente e toolkit per analitica."
    keywords="strumenti di ricerca di scienze dati, macchina virtuale scienza dati e strumenti per la ricerca di scienze dati, scienza dati linux"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="bradsev" />


# <a name="introduction-to-the-cloud-based-data-science-virtual-machine-for-linux-and-windows"></a>Introduzione a basate su cloud dati scienza macchina virtuale per Linux e Windows

Dati scienza Virtual Machine è un'immagine personalizzata macchine Virtuali nel cloud di Microsoft Azure progettate appositamente per eseguire una ricerca di scienze di dati. Ha molti dati comuni scienza e altri strumenti preinstallato e preconfigurato in modo da iniziare subito la creazione di applicazioni intelligente per analitica avanzate. È disponibile in Windows Server 2012 o in versioni basate su OpenLogic 7.2 CentOS Linux. 

In questo argomento viene illustrato cosa può fare con macchine Virtuali di scienze dati, vengono illustrate alcune procedure illustrati gli scenari principali per l'uso della macchina virtuale, consente di definire diverse caratteristiche chiave disponibile per le versioni di Windows e Linux e fornisce istruzioni su come iniziare a utilizzare loro.


## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>Cosa è possibile fare con la macchina virtuale scienza di dati

L'obiettivo di dati scienza Virtual Machine è fornire personale dati tutti i livelli di competenza e i ruoli con un ambiente di scienze multibrowser da dati. Questa macchina virtuale di risparmiare molto tempo che spendere aveva distribuiti un ambiente comparabile autonomamente. Se, tuttavia, avviare immediatamente il progetto di scienze dei dati in un'istanza di macchine Virtuali appena creata. 

Macchine Virtuali di scienze dati è progettato e configurato per l'utilizzo di un scenari di utilizzo generali. È possibile adattare l'ambiente verso l'alto o verso il basso come alle esigenze del progetto. In grado di utilizzare la lingua preferita alle attività di ricerca di scienze dati programma. È possibile installare altri strumenti e personalizzare il sistema per le proprie esigenze.
 
## <a name="key-scenarios"></a>Scenari principali
Questa sezione propone alcuni scenari principali per il quale è possibile distribuire macchine Virtuali di scienze dati.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Desktop analitica preconfigurato nel cloud

Macchine Virtuali di scienze dati offre una configurazione di base per il team di scienze dati per sostituire un desktop gestito cloud desktop locale. Previsione assicura che tutti gli scienziati dati in un team una configurazione coerenza con cui si desidera verificare esperimenti e promuovere la collaborazione. Si riduce i costi riducendo il carico di lavoro sysadmin e salvare sul tempo necessario per valutare, installare e gestire i vari pacchetti software necessari per eseguire analitica avanzate.  

### <a name="data-science-training-and-education"></a>L'istruzione e formazione di scienze di dati

Istruttori Enterprise e docenti che spiegano scienza dati forniscono in genere un'immagine di macchina virtuale per assicurarsi che gli studenti hanno una configurazione coerenza e che gli esempi di funzionino prevedibili. Macchine Virtuali di scienze dati crea un ambiente su richiesta con una configurazione coerenza che facilita i problemi di supporto e problemi di compatibilità. Casi in cui questi ambienti devono essere generati frequentemente, in particolare per più breve corsi di formazione, benefit sostanzialmente.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Capacità flessibile su richiesta per i progetti di grandi dimensioni

Dati scienza hackathons/gare o modellazione dei dati di grandi dimensioni e l'esplorazione richiedono scalabilità capacità hardware, in genere per brevità. Macchine Virtuali di scienze dati può essere utile replicare l'ambiente di scienze dati rapidamente su richiesta nel server in scala fuori che consentono di esperimenti che richiedono risorse di elaborazione preferite da eseguire.

### <a name="short-term-experimentation-and-evaluation"></a>Valutazione e sperimentazione a breve termine

Macchine Virtuali di scienze dati può essere usato per valutare o informazioni su strumenti, ad esempio Microsoft R di SQL Server, strumenti di Visual Studio, Jupyter, formazione sui toni del / ML Toolkit e nuovi strumenti popolari nella community di minime configurazione basata. Poiché la macchina virtuale scienza dati può essere configurata rapidamente, possono essere applicata in altri scenari di utilizzo a breve termine, ad esempio la replica esperimenti pubblicati, esecuzione demo, seguenti procedure dettagliate in sessioni online o conferenza esercitazioni.


## <a name="whats-included-in-the-data-science-vm"></a>Cosa è incluso in macchine Virtuali di scienze dati?

Dati scienza Virtual Machine include numerosi strumenti di ricerca di scienze popolari dati già installato e configurato. Include inoltre strumenti che rendono più semplice lavorare con i diversi prodotti Azure, dati e analitica. È possibile esplorare e creare modelli di Office nel set di dati su larga scala utilizzando Microsoft R Server o SQL Server 2016. Una serie di altri strumenti dalla community di Apri origine e di Microsoft sono i blocchi appunti e codice inclusi, così come esempio. Nella tabella seguente consente di definire diverse e confronta i principali componenti inclusi nelle edizioni di Windows e Linux di scienze Virtual Machine dati.


|**Edizione di Windows** | **Linux Edition** |
|----------------|---------------|
|Microsoft R Server Developer Edition | Microsoft R Server Developer Edition |
|Anaconda Python 2.7, 3.5 | Anaconda Python 2.7, 3.5 |
|Blocco appunti Jupyter Server (R, Python) | JupyterHub: I blocchi appunti multiutente Jupyter (R, Python, Olga) |
|SQL Server 2016 Developer Edition: Scalable nel database analitica con i servizi di R | Postgres, esce SQL (strumento database), driver di SQL Server e riga di comando (bcp, sqlcmd) |
|Visual Studio Community Edition 2015 (IDE) </br> -Azure HDInsight (Hadoop) dati Lake, SQL Server Data tools </br> -R Node e Python tools per Visual Studio |IDE ed editor </br> -Eclisse con Azure toolkit plug-in </br> -Emacs (con ESS auctex) gedit |
|Desktop di Power BI | -- |
|Strumenti di apprendimento computer </br> -Integrazione con apprendimento Azure </br> -CNTK (complete risorse/AI) </br> -Xgboost (impostazioni generali ML strumento gare scienza dati) </br> -Wabbit Vowpal (studente rapidamente online) </br> -Rattle (visiva dei dati di avvio rapido e strumento analitica) </br> -Mxnet (complete risorse/AI) | Strumenti di apprendimento computer </br> -Integrazioni con apprendimento Azure </br> -CNTK (complete risorse/AI) </br> -Xgboost (impostazioni generali ML strumento gare scienza dati) </br> -Wabbit Vowpal (studente rapidamente online) </br> -Rattle (visiva dei dati di avvio rapido e strumento analitica)  |
| SDK per accedere a Azure e Cortana Intelligence famiglia di servizi | SDK per accedere a Azure e Cortana Intelligence famiglia di servizi |
| Strumenti per lo spostamento dei dati e la gestione delle risorse Azure e dati: Esplora archivi Azure, CLI, PowerShell, AdlCopy (Azure dati Lake), AzCopy, dtui (per DocumentDB), il Gateway di gestione dati Microsoft | Strumenti per lo spostamento dei dati e la gestione delle risorse Azure e dati: Esplora archivi Azure, CLI |
| Operazioni, plug-in Visual Studio Team Services | Operazioni |
| Porta di Windows di più popolari utilità della riga di comando di Linux/Unix accessibile tramite GitBash/prompt dei comandi | -- |



## <a name="how-to-get-started-with-the-windows-data-science-vm"></a>Come iniziare con la macchina virtuale scienza di dati di Windows

- Crea un'istanza di macchine Virtuali in Windows, passare a [questa pagina](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) e selezionare il pulsante **Crea macchina virtuale** verde.
- Accedere a macchina virtuale dal desktop remoto utilizzando le credenziali specificate al momento della creazione della macchina virtuale.
- Per trovare e avviare gli strumenti disponibili, fare clic sul menu **Start** .


## <a name="get-started-with-the-linux-data-science-vm"></a>Guida introduttiva a macchina virtuale di scienze dati Linux

- Creare un'istanza della macchina virtuale su Linux (in base OpenLogic CentOS) passando alla [pagina](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/) e selezionare il pulsante **Crea macchina virtuale** .
- Accedere a macchina virtuale da un client SSH, ad esempio Putty o SSH comando usando le credenziali specificato al momento della creazione della macchina virtuale.
- Prompt dei comandi shell immettere informazioni di altre dsvm.
- Per un desktop con interfaccia grafico, scaricare il client X2Go per la piattaforma client [qui](http://wiki.x2go.org/doku.php/doc:installation:x2goclient) e seguire le istruzioni del documento macchine Virtuali di scienze dati Linux [disposizione Linux dati scienza Virtual Machine](machine-learning-data-science-linux-dsvm-intro.md#installing-and-configuring-x2go-client).


## <a name="next-steps"></a>Passaggi successivi

### <a name="for-the-windows-data-science-vm"></a>Per la scienza di dati macchine Virtuali di Windows

- Per ulteriori informazioni su come eseguire specifici strumenti disponibili nella versione di Windows, vedere [disposizione Microsoft dati scienza Virtual Machine](machine-learning-data-science-provision-vm.md) e
-  Per ulteriori informazioni su come eseguire varie attività necessarie per il progetto di scienze dati nella macchina virtuale di Windows, vedere [dieci operazioni da eseguire sulla ricerca di scienze dati macchina virtuale](machine-learning-data-science-vm-do-ten-things.md).

### <a name="for-the-linux-data-science-vm"></a>Per la ricerca di scienze Linux dati macchine Virtuali

- Per ulteriori informazioni su come eseguire specifici strumenti disponibili nella versione Linux, vedere [disposizione Linux dati scienza Virtual Machine](machine-learning-data-science-linux-dsvm-intro.md).
- Per una procedura dettagliata che illustra come eseguire diverse attività comuni di dati scienza con VM Linux, vedere [ricerca di scienze di dati in Linux dati scienza Virtual Machine](machine-learning-data-science-linux-dsvm-walkthrough.md).
