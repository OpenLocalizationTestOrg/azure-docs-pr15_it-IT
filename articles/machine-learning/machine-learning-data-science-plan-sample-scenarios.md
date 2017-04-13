<properties
    pageTitle="Scenari per Analitica avanzate processo e la tecnologia di apprendimento Azure | Microsoft Azure"
    description="Selezionare gli scenari appropriati per eseguire queste avanzate stima analitica con il processo di scienze dei dati del Team."
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
    ms.date="09/19/2016" 
    ms.author="bradsev" />


# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Scenari per analitica avanzate in apprendimento Azure

Questo articolo illustra i diversi tipi di origini dati di esempio e scenari di destinazione che possono essere gestiti dal [Team dati scienza processo (TDSP)](data-science-process-overview.md). Il TDSP fornisce un approccio sistematico ai team di collaborare sulla creazione di applicazioni intelligente. Gli scenari presentati di seguito illustrano le opzioni disponibili del flusso di lavoro di elaborazione dei dati che dipendono le caratteristiche dei dati, le posizioni di origine e archivi di destinazione in Azure.

L' **albero decisionale** per la selezione gli scenari di esempio che è appropriato per i dati e l'obiettivo è disponibile nella sezione precedente.

>[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


Ognuna delle sezioni seguenti presenta uno scenario di esempio. Per ogni scenario, una ricerca di scienze dati possibili o analitica avanzate flusso e Azure risorse di supporto sono elencati.

>[AZURE.NOTE]**Per tutti gli scenari seguenti, è necessario:**
><br/>
>* [Creare un account di archiviazione](../storage/storage-create-storage-account.md)
><br/>
>* [Creare un'area di lavoro di apprendimento Azure](machine-learning-create-workspace.md)


## <a name="smalllocal"></a>Scenario \#1: piccole e medie set di dati tabulari in un file locali

![Piccole e medie file locali][1]

#### <a name="additional-azure-resources-none"></a>Altre risorse Azure: nessuna

1.  Accedere al [computer Azure formazione Studio](https://studio.azureml.net/).

2.  Caricare un set di dati.

3.  Creare un flusso di prova di apprendimento Azure iniziando DataSet caricato.

## <a name="smalllocalprocess"></a>Scenario \#2: piccole e medie set di dati di file locali che richiedono l'elaborazione

![Piccole e medie file locali con elaborazione][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Altre risorse Azure: Azure macchina virtuale (server blocco appunti IPython)

1.  Creare una macchina virtuale Azure esecuzione IPython blocco appunti.

2.  Caricare dati in un contenitore di archiviazione Azure.

3.  Pre-elaborazione e la pulizia dei dati nel blocco appunti IPython, accesso ai dati dal contenitore di archiviazione Azure.

4.  Trasformare i dati in pulito, sotto forma di tabella.

5.  Salvare i dati trasformati in BLOB Azure.

6.  Accedere al [computer Azure formazione Studio](https://studio.azureml.net/).

7.  Leggere i dati dal BLOB Azure utilizzando i [Dati di importazione] [ import-data] modulo.

8. Creare un flusso di prova di apprendimento Azure iniziando DataSet acquisiti.

## <a name="largelocal"></a>Scenario \#3: ampio set di dati di file locali, l'assegnazione del BLOB Azure

![File di grandi dimensioni][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Altre risorse Azure: Azure macchina virtuale (server blocco appunti IPython)

1.  Creare una macchina virtuale Azure esecuzione IPython blocco appunti.

2.  Caricare dati in un contenitore di archiviazione Azure.

3.  Pre-elaborazione e la pulizia dei dati nel blocco appunti IPython, accesso ai dati da BLOB Azure.

4.  Trasformare i dati pulire tabulare, se necessario.

5.  Esplorare i dati e creare caratteristiche in base alle esigenze.

6.  Estrarre un campione di dati di piccole e medie.

7.  Salvare i dati campionati nei blob Azure.

8. Accedere al [computer Azure formazione Studio](https://studio.azureml.net/).

9. Leggere i dati dal BLOB Azure utilizzando i [Dati di importazione] [ import-data] modulo.

10. Definire il flusso di prova apprendimento Azure iniziando DataSet acquisiti.


## <a name="smalllocaltodb"></a>Scenario \#4: piccole e medie set di dati di file locali, l'assegnazione del SQL Server in un computer Virtal Azure

![Piccole e medie file locali al database SQL di Azure][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Altre risorse Azure: Azure macchina virtuale (SQL Server / server blocco appunti IPython)

1.  Creare una macchina virtuale Azure che esegue SQL Server + blocco appunti IPython.

2.  Caricare dati in un contenitore di archiviazione Azure.

3.  Pre-elaborazione e la pulizia dei dati nel contenitore di archiviazione Azure utilizzo IPython blocco appunti.

4.  Trasformare i dati pulire tabulare, se necessario.

5.  Salvare i dati in file locale macchine Virtuali (IPython blocco appunti è in esecuzione in macchine Virtuali, unità locali fanno riferimento a unità macchine Virtuali).

6.  Caricare dati nel database di SQL Server in esecuzione in una macchina virtuale Azure.

    Opzione \#1: con SQL Server Management Studio.

    - Accesso a SQL Server macchine Virtuali
    - Esecuzione di SQL Server Management Studio.
    - Creare tabelle di database e di destinazione.
    - Utilizzare uno in blocco metodi per caricare i dati da file locale macchine Virtuali di importazione.

    Opzione \#2: usare IPython blocco appunti, non è consigliabile per medie e grandi set di dati<!-- -->    
    - Consente di accedere a SQL Server in macchine Virtuali stringa di connessione ODBC.
    - Creare tabelle di database e di destinazione.
    - Utilizzare uno in blocco metodi per caricare i dati da file locale macchine Virtuali di importazione.

7.  Esplorare i dati, creare caratteristiche in base alle esigenze. Si noti che non è necessario che le caratteristiche materializzare nelle tabelle di database. Nota solo la query necessaria per crearle.

8. Scegliere una dimensione del campione dei dati, se necessario e/o bene accolta.

9. Accedere al [computer Azure formazione Studio](https://studio.azureml.net/).

10. Leggere i dati direttamente da SQL Server utilizzando i [Dati di importazione] [ import-data] modulo. Incollare la query necessaria che estrae i campi, crea caratteristiche ed esempi dati eventualmente direttamente nella finestra [Importa dati] [ import-data] query.

11. Definire il flusso di prova apprendimento Azure iniziando DataSet acquisiti.

## <a name="largelocaltodb"></a>Scenario \#5: ampio set di dati in un file locali destinazione SQL Server in macchine Virtuali di Azure

![File locali di grandi dimensioni al database SQL di Azure][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Altre risorse Azure: Azure macchina virtuale (SQL Server / server blocco appunti IPython)

1.  Creare una macchina virtuale Azure che esegue SQL Server e IPython blocco appunti.

2.  Caricare dati in un contenitore di archiviazione Azure.

3.  (Facoltativo) Pre-elaborazione e la pulizia dei dati.

    un.  Pre-elaborazione e la pulizia dei dati nel blocco appunti IPython, accesso ai dati da BLOB Azure.

    b.  Trasformare i dati pulire tabulare, se necessario.

    c.  Salvare i dati in file locale macchine Virtuali (IPython blocco appunti è in esecuzione in macchine Virtuali, unità locali fanno riferimento a unità macchine Virtuali).

4.  Caricare dati nel database di SQL Server in esecuzione in una macchina virtuale Azure.

    un.  Accesso a SQL Server macchine Virtuali.

    b.  Se i dati non salvati già, scaricare i file di dati dal contenitore di archiviazione Azure cartella macchine Virtuali locale.

    c.  Esecuzione di SQL Server Management Studio.

    d.  Creare tabelle di database e di destinazione.

    e.  Utilizzare uno in blocco importare metodi per caricare i dati.

    f.  Se sono necessari i join delle tabelle, creare indici per accelerare join.

     > [AZURE.NOTE] Per il caricamento più veloce delle dimensioni dei dati di grandi dimensioni, è consigliabile che si creano tabelle partizionate e massa importare i dati in parallelo. Per ulteriori informazioni, vedere [In parallelo importazione dei dati in tabelle suddiviso SQL](machine-learning-data-science-parallel-load-sql-partitioned-tables.md).

5.  Esplorare i dati, creare caratteristiche in base alle esigenze. Si noti che non è necessario che le caratteristiche materializzare nelle tabelle di database. Nota solo la query necessaria per crearle.

6.  Scegliere una dimensione del campione dei dati, se necessario e/o bene accolta.

7.  Accedere al [computer Azure formazione Studio](https://studio.azureml.net/).

8. Leggere i dati direttamente da SQL Server utilizzando i [Dati di importazione] [ import-data] modulo. Incollare la query necessaria che estrae i campi, crea caratteristiche ed esempi dati eventualmente direttamente nella finestra [Importa dati] [ import-data] query.

9. Rendiconto di prova apprendimento Azure semplice iniziando caricati set di dati

## <a name="largedbtodb"></a>Scenario \#6: ampio set di dati in un Server SQL database locale, l'assegnazione del SQL Server in una macchina virtuale Azure

![SQL di grandi dimensioni DB locale al database SQL di Azure][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Altre risorse Azure: Azure macchina virtuale (SQL Server / server blocco appunti IPython)

1.  Creare una macchina virtuale Azure che esegue SQL Server e IPython blocco appunti.

2.  Utilizzare uno dei dati è possibile esportare i metodi per esportare i dati da SQL Server in file di dettagli.

    > [AZURE.NOTE] Se si decide di spostare tutti i dati dal database locale, un metodo alternativo (più veloce) per spostare il database completo per l'istanza di SQL Server in Azure. Ignorare i passaggi per esportare i dati, creare database e carico/importare i dati nel database di destinazione e seguire il metodo alternativo.

3.  Caricare i file di immagine al contenitore di archiviazione Azure.

4.  Caricare i dati a un database di SQL Server che esegue una macchina virtuale Azure.

    un.  Accesso a SQL Server macchine Virtuali.

    b.  Scaricare i file di dati da un contenitore di archiviazione Azure nella cartella macchine Virtuali locale.

    c.  Esecuzione di SQL Server Management Studio.

    d.  Creare tabelle di database e di destinazione.

    e.  Utilizzare uno in blocco importare metodi per caricare i dati.

    f.  Se sono necessari i join delle tabelle, creare indici per accelerare join.

    > [AZURE.NOTE] Per il caricamento di più veloce delle dimensioni dei dati di grandi dimensioni, creare tabelle partizionate e massa importare i dati in parallelo. Per ulteriori informazioni, vedere [In parallelo importazione dei dati in tabelle suddiviso SQL](machine-learning-data-science-parallel-load-sql-partitioned-tables.md).

5.  Esplorare i dati, creare caratteristiche in base alle esigenze. Si noti che non è necessario che le caratteristiche materializzare nelle tabelle di database. Nota solo la query necessaria per crearle.

6.  Scegliere una dimensione del campione dei dati, se necessario e/o bene accolta.

7.  Accedere al [computer Azure formazione Studio](https://studio.azureml.net/).

8. Leggere i dati direttamente da SQL Server utilizzando i [Dati di importazione] [ import-data] modulo. Incollare la query necessaria che estrae i campi, crea caratteristiche ed esempi dati eventualmente direttamente nella finestra [Importa dati] [ import-data] query.

9. Apprendimento Azure semplice sperimentare flusso iniziando caricati set di dati.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Metodo alternativo per copiare un intero database da un Server SQL locale a Database SQL Azure

![Disconnettersi DB locale e connessione al database SQL di Azure][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Altre risorse Azure: Azure macchina virtuale (SQL Server / server blocco appunti IPython)

Per replicare l'intero database di SQL Server in macchine Virtuali di Server il SQL, è necessario copiare un database da un percorso/server in un'altra, presupponendo che il database è possibile eseguire temporaneamente non in linea. A tale scopo in SQL Server Management Studio oggetto Explorer o utilizzare i comandi Transact-SQL equivalenti.

1. Scollegare il database in corrispondenza della posizione di origine. Per ulteriori informazioni, vedere [Scollega un database](https://technet.microsoft.com/library/ms191491(v=sql.110).aspx).
2. Nella finestra di Esplora risorse o prompt dei comandi di Windows, copiare il database scollegato o file e file di log o i file nel percorso di destinazione nella macchina virtuale SQL Server in Azure.
3. Allegare file copiati all'istanza di SQL Server di destinazione. Per ulteriori informazioni, vedere [metodo per allegare un Database](https://technet.microsoft.com/library/ms190209(v=sql.110).aspx).

[Spostare un Database utilizzando scollegare e allegare (Transact-SQL)](https://technet.microsoft.com/library/ms187858(v=sql.110).aspx)

## <a name="largedbtohive"></a>Scenario \#7: dati locali dei file, come destinazione database Hive cluster Azure HDInsight Hadoop

![Dati in locale target Hive][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>Altre risorse Azure: Azure HDInsight Hadoop Cluster e Azure Virtual Machine (server blocco appunti IPython)

1.  Creare una macchina virtuale Azure che eseguono server IPython blocco appunti.

2.  Creare un cluster di Azure HDInsight Hadoop.

3.  (Facoltativo) Pre-elaborazione e la pulizia dei dati.

    un.  Pre-elaborazione e la pulizia dei dati nel blocco appunti IPython, accesso ai dati da BLOB Azure.

    b.  Trasformare i dati pulire tabulare, se necessario.

    c.  Salvare i dati in file locale macchine Virtuali (IPython blocco appunti è in esecuzione in macchine Virtuali, unità locali fanno riferimento a unità macchine Virtuali).

4.  Caricare dati contenitore predefinito del cluster Hadoop selezionato nel passaggio 2.

5.  Caricare dati nel database di Hive cluster Azure HDInsight Hadoop.

    un.  Accedere al nodo principale del cluster Hadoop

    b.  Aprire la riga di comando Hadoop.

    c.  Immettere la directory radice Hive dal comando `cd %hive_home%\bin` nella riga di comando di Hadoop.

    d.  Eseguire la query Hive per creare database e le tabelle e caricare dati dallo spazio di archiviazione blob alle tabelle Hive.

    > [AZURE.NOTE] Se i dati sono grandi, gli utenti possono creare la tabella Hive con partizioni. Quindi, gli utenti possono utilizzare un `for` Esegui ciclo continuo in Hadoop riga di comando sul nodo principale per caricare dati nella tabella Hive suddivise da partizione.

6.  Esplorare i dati e creare caratteristiche in base alle esigenze nella riga di comando di Hadoop. Si noti che non è necessario che le caratteristiche materializzare nelle tabelle di database. Nota solo la query necessaria per crearle.

    un.  Accedere al nodo principale del cluster Hadoop

    b.  Aprire la riga di comando Hadoop.

    c.  Immettere la directory radice Hive dal comando `cd %hive_home%\bin` nella riga di comando di Hadoop.

    d.  Eseguire la query Hive nella riga di comando di Hadoop sul nodo principale del cluster Hadoop per esplorare i dati e creare caratteristiche in base alle esigenze.

7.  Se necessario e/o lo si desidera, i dati in modo da adattarlo Azure Machine Learning Studio di esempio.

8.  Accedere al [computer Azure formazione Studio](https://studio.azureml.net/).

9. Leggere i dati direttamente dalla `Hive Queries` utilizzando i [Dati di importazione] [ import-data] modulo. Incollare la query necessaria che estrae i campi, crea caratteristiche ed esempi dati eventualmente direttamente nella finestra [Importa dati] [ import-data] query.

10. Apprendimento Azure semplice sperimentare flusso iniziando caricati set di dati.

## <a name="decisiontree"></a>Albero decisionale per la selezione di uno scenario
------------------------

Nel diagramma seguente vengono riepilogati gli scenari descritti sopra e il processo di Analitica avanzate e tecnologia scelte che consentono di accedere a tutti gli scenari dettagliati. Si noti che potrebbero essere necessari elaborazione dei dati, esplorazione, Progettazione funzionalità e campioni posizionare in uno o più metodo/ambiente - nell'origine, intermedio, e/o ambienti di destinazione – e può procedere in modo iterativo, in base alle esigenze. Il diagramma solo funge da un'illustrazione di alcune delle possibili flussi e non forniscono enumerazione completa.

![Scenari di esempio DS processo procedura dettagliata][8]

### <a name="advanced-analytics-in-action-examples"></a>Avanzate Analitica in azione esempi

Per procedure dettagliate per l'apprendimento Azure-to-end che utilizzano il processo Analitica avanzate e la tecnologia utilizzando set di dati pubblici, vedere:


* [Team dati scienza processo in azione: utilizzo di SQL Server](machine-learning-data-science-process-sql-walkthrough.md).
* [Team dati scienza processo in azione: utilizzo dei cluster HDInsight Hadoop](machine-learning-data-science-process-hive-walkthrough.md).


[1]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
