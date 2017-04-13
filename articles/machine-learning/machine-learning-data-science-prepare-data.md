<properties
    pageTitle="Attività per preparare i dati per il avanzato lavorare risorse | Microsoft Azure"
    description="Pre-elaborazione e la pulizia dei dati per la preparazione per l'apprendimento."
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


# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Attività per preparare i dati per il avanzato lavorare risorse

Pre-elaborazione e la pulizia dei dati sono attività importanti che in genere devono essere effettuate prima di utilizzare set di dati in modo efficace per l'apprendimento. Dati non elaborati sono spesso disturbata e origini non affidabili e potrebbero mancare valori. Uso di tali dati per una modellazione può generare risultati fuorvianti. Queste attività fanno parte del Team dati scienza processo (TDSP) e in genere seguono un'esplorazione iniziale di un set di dati utilizzato per individuare e pianificare pre-elaborazione obbligatorio. Per informazioni più dettagliate sul processo di TDSP, vedere i passaggi descritti nel [Processo di scienze dei dati del Team](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

Pre-elaborazione e la pulizia di attività, ad esempio l'attività di esplorazione dei dati può avvenire in una vasta gamma di ambienti, ad esempio SQL o Hive o Azure Machine Learning Studio e con diversi strumenti e lingue, ad esempio R o Python, a seconda del tipo in cui sono memorizzati i dati e la modalità di formattazione. Poiché TDSP iterativo natura, queste attività possono essere eseguito in varie fasi del processo del flusso di lavoro.

In questo articolo vengono presentati diversi concetti di elaborazione dei dati e le attività che possono essere eseguite prima o dopo il caricamento di dati in apprendimento Azure.

Per un esempio di esplorazione dei dati e pre-elaborazione effettuate all'interno di studio di apprendimento Azure, vedere il video [pre-elaborazione dei dati di Azure Machine Learning Studio](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) .


## <a name="why-pre-process-and-clean-data"></a>Perché pre-elaborazione e pulizia dati?

Raccolta da diverse origini dati reali e processi e pertanto potrebbe contenere irregolarità o dati danneggiati compromettere la qualità del set di dati. Problemi di qualità tipica dati che si verificano sono:

* **Incompleto**: dati non contengono attributi o che contiene valori mancanti.
* **Noisy**: dati non contengono record errato o valori erratici.
* **Incoerenti**: dati non contengono record in conflitto o differenze.

Dati di qualità sono un prerequisito per i modelli di previsione qualità. Per evitare "propagazione delle limitazioni della fuori" e migliorare la qualità dei dati e pertanto le prestazioni del modello, è fondamentale per condurre una schermata di integrità dei dati per individuare i problemi di dati in anticipo e decidere di elaborazione dei dati corrispondenti e i passaggi per la pulizia.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Quali sono alcune schermate di integrità dei dati in genere utilizzate?

È possibile controllare la qualità generale di dati selezionando:

* Il numero di **record**.
* Il numero di **attributi** (o **caratteristiche**).
* Attributo **tipi di dati** (nominale, ordinale o continuo).
* Il numero di **valori mancanti**.
* **Corretto** dei dati.
    * Se i dati in TSV o CSV, verificare che i separatori di colonna e i separatori di riga sempre correttamente separano di colonne e righe.
    * Se i dati sono in formato HTML o XML, verificare se i dati sia corretto basati sulle rispettive standard.
    * Analisi potrebbe anche essere necessario per estrarre informazioni strutturate dai dati semistrutturati o non strutturati.
* **Record di dati non coerente**. Selezionare l'intervallo di valori consentiti. ad esempio, se i dati contengono GPA studente, verificare se la media GPA è compreso nell'intervallo specificato, ad esempio 0 ~ 4.

Quando si verificano problemi con i dati, **elaborazione passaggi** sono necessari che prevede spesso pulizia valori mancanti, normalizzazione dei dati, discretizzazione, elaborazione per rimuovere e/o sostituire caratteri incorporati che possono influire sull'allineamento dei dati di testo, tipi di dati in comune tipi di campi e le altre impostazioni.

**Apprendimento azure fruisce dei dati in formato tabulare corretti**.  Se i dati sono già in formato tabulare, pre-elaborazione dei dati può essere eseguite direttamente con Azure l'apprendimento di Studio di risorse computer.  Se i dati non sono in formato tabulare, ad esempio si trova nel XML, potrebbe essere necessario per convertire i dati sotto forma di analisi.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Quali sono alcune delle principali attività pre-elaborazione di dati?

* **Pulizia dei dati**: compilare o valori mancanti, rilevare e rimuovere dati disturbati e valori erratici.
* **Trasformazione dei dati**: normalizzare i dati per ridurre le dimensioni e rumore.
* **Riduzione di dati**: esempio record di dati o gli attributi per la gestione dei dati più semplice.
* **Discretizzazione dati**: convertire gli attributi continui gli attributi di categoria per semplificarne l'utilizzo con alcuni metodi di risorse del computer.
* **Testo pulizia**: rimuovere i caratteri incorporati che possono causare problemi di allineamento dei dati, per ad esempio schede incorporate in un file di dati delimitati da tabulazioni, incorporato nuove righe che possono causare il danneggiamento record e così via.

Nelle sezioni seguenti vengono illustrate in dettaglio alcune di queste operazioni di elaborazione dei dati.

## <a name="how-to-deal-with-missing-values"></a>Come è possibile gestire i valori mancanti?

Per gestire i valori mancanti, è preferibile individuare il motivo per cui i valori mancanti da gestire meglio il problema. Metodi di gestione valore mancanti tipici sono:

* **Eliminazione**: rimuovere i record con valori mancanti
* **Sostituzione fittizio**: sostituire i valori mancanti con un valore fittizio: _sconosciuto_ per categoria o uguale a 0, ad esempio per valori numerici.
* **Significato sostituzione**: se i dati mancanti sono numerici, sostituire i valori mancanti con la Media.
* **Sostituzione frequente**: se i dati mancanti per categoria, sostituire i valori mancanti con l'elemento più frequente
* **Sostituzione di regressione**: consente di sostituire i valori mancanti con valori regressioni di un metodo di regressione.  

## <a name="how-to-normalize-data"></a>Come è possibile normalizzare i dati?

Normalizzazione dei dati scale nuovamente i valori numerici in un intervallo specificato. Metodi di dati più diffusi normalizzazione includono:

* **Min-Max normalizzazione**: lineare trasformare i dati in un intervallo, ad esempio compreso tra 0 e 1, in cui il valore minimo viene ridimensionato al valore massimo e 0 e 1.
* **Punteggio Z normalizzazione**: ridurre i dati in base a Media e deviazione standard: dividere la differenza tra i dati e la media per la deviazione standard.
* **Ridimensionamento decimale**: ridurre i dati spostando il separatore decimale del valore dell'attributo.  

## <a name="how-to-discretize-data"></a>Come la discretizzazione dati?

Dati possono essere discretizzati mediante la conversione di valori continui agli attributi nominali o intervalli. Sono alcuni modi in questo modo:

* **Larghezza uguale Binning**: dividere l'intervallo di tutti i valori possibili di un attributo in gruppi N della stessa dimensione e assegnare i valori che rientrano in una classe con il numero di bin.
* **Altezza uguale Binning**: dividere l'intervallo di tutti i valori possibili di un attributo in gruppi N, ognuno contenente lo stesso numero di istanze, quindi assegnare i valori che rientrano in una classe con il numero di bin.  

## <a name="how-to-reduce-data"></a>Come è possibile ridurre i dati?

Sono disponibili diversi metodi per ridurre le dimensioni di dati per i dati più facile gestione. A seconda delle dimensioni dei dati e il dominio, è possono applicare i metodi seguenti:

* **Esempio di record**: i record di dati di esempio e scegliere solo il subset representative dai dati.
* **Attributo campioni**: selezionare solo un sottoinsieme degli attributi più importanti dai dati.  
* **Aggregazione**: dividere i dati in gruppi e archiviare i numeri per ogni gruppo. I numeri di ricavi giornaliera di una serie di ristorante negli ultimi 20 anni, ad esempio, possono essere aggregati a ricavi mensili per ridurre le dimensioni dei dati.  

## <a name="how-to-clean-text-data"></a>Come è possibile eliminare i dati di testo?

**Campi di testo di dati di tabella** può includere caratteri che influiscono su limiti di allineamento e/o il record di colonne. Ad esempio incorporato schede in un problema di allineamento file delimitati da tabulazioni colonna causa e incorporato caratteri della nuova riga interrompere le righe di record. Gestione di codifica di testo non corretta durante la scrittura/lettura testo comporta la perdita di dati, non intenzionale introduzione di caratteri non leggibili, ad esempio, LastVacationDate e possono inoltre influenza analisi. Analisi attenzione e la modifica potrebbe essere necessari per pulire i campi di testo per l'allineamento corretto e/o ai dati strutturato estratto dai dati di testo non strutturati o semistrutturati.

**Esplorazione dei dati** offre una visualizzazione iniziale dei dati. Un numero di problemi con i dati può essere scoperte durante questo passaggio e metodi corrispondenti possono essere applicati per risolvere i problemi.  È importante porre domande, ad esempio qual è l'origine del problema e come il problema potrebbe essere stata introdotta. Questa operazione anche consente di stabilire i passaggi di elaborazione dei dati che occorre eseguire per risolverli. Il tipo di informazioni approfondite uno intende derivano dai dati è utilizzabile anche per definire la priorità delle attività di elaborazione dei dati.

## <a name="references"></a>Riferimenti

>*Data Mining: concetti e le tecniche*, terza edizione, Morgan Kaufmann, 2011 Jiawei Han, Micheline Kamber e Jian Pei
