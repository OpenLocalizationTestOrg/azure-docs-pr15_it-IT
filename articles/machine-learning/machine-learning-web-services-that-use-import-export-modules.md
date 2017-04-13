<properties
    pageTitle="Distribuzione di servizi web di Azure ML che utilizzano moduli dati importazione / esportazione dati | Microsoft Azure"
    description="Informazioni su come utilizzare i moduli dati Importa ed Esporta dati per inviare e ricevere dati da un servizio web."
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondlaghaeian"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="v-donglo"/>



# <a name="deploying-azure-ml-web-services-that-use-data-import-and-data-export-modules"></a>Distribuzione di servizi web di Azure ML che utilizzano importazione dei dati e moduli di esportazione dei dati 

Quando si crea una prova di Office, è in genere aggiungere un input di servizio web e l'output. Quando si distribuisce il prova, gli utenti possono inviare e ricevere dati dal servizio web tramite input e output. Per alcune applicazioni, dati del consumer disponibile da un feed di dati o si trovano già nell'origine dati esterna, ad esempio archiviazione Blob Azure. In questi casi, non è necessario leggere e scrivere dati utilizzando output e input di servizio web. Possono, invece, utilizzare il servizio di esecuzione Batch (BES) per leggere i dati dell'origine dati mediante un modulo di importare dati e scrivere i risultati punteggio in una posizione di dati diversi mediante un modulo di esportare i dati.

Importare dati di esportazione dati moduli possibile leggere e scrivere a un numero di posizioni, ad esempio un URL Web tramite HTTP, una Query Hive, un database SQL Azure, archivio tabelle Azure, archiviazione Blob Azure, un Feed di dati forniscono dati o un database SQL in locale.

Questo argomento viene utilizzata la "esempio 5: Evaluate treno, Test, per la classificazione binaria: adulto set di dati" di esempio e si presuppone che il set di dati è già stato caricato in una tabella di SQL Azure denominata censusdata.

## <a name="create-the-training-experiment"></a>Creare la prova di formazione 
 
Quando si apre la "esempio 5: Evaluate treno, Test, per la classificazione binaria: set di dati adulto" esempio viene utilizzato il set di dati di esempio adulto censimento reddito binario classificazione. E prova nell'area di lavoro sarà simile a nella figura seguente.

![Configurazione iniziale di prova.](./media/machine-learning-web-services-that-use-import-export-modules/initial-look-of-experiment.png)
  

Per leggere i dati della tabella di SQL Azure:

1.  Eliminare il modulo di set di dati.
2.  Nella casella di ricerca componenti digitare importazione.
3.  Dall'elenco dei risultati, aggiungere un modulo di *Importare dati* all'area di prova.
4.  Connettere l'output del modulo di *Importare dati* di input del modulo *Dati mancanti libera* .
5.  Nel riquadro Proprietà selezionare **Il Database di SQL Azure** nell'elenco a discesa **Origine dati** .
6.  Il **nome del server di Database**, **nome del Database**, **nome utente**e **Password** campi, immettere le informazioni appropriate per il database.
7.  Nel campo della query di Database, immettere la query seguente.

        select [age],
           [workclass],
           [fnlwgt],
           [education],
           [education-num],
           [marital-status],
           [occupation],
           [relationship],
           [race],
           [sex],
           [capital-gain],
           [capital-loss],
           [hours-per-week],
           [native-country],
           [income]
        from dbo.censusdata;

8.  Nella parte inferiore della prova area di lavoro, fare clic su **Esegui**.

## <a name="create-the-predictive-experiment"></a>Creare prova previsione

Quindi configurare prova previsione da cui distribuire il servizio web.

1.  Nella parte inferiore dell'area di prova, fare clic **Su servizio Web** e selezionare **Servizio Web previsione (scelta consigliata)**.
2.  Rimuovere gli *Input di servizio Web* e *moduli di Output servizio Web* da prova previsione. 
3.  Nella casella di ricerca componenti digitare Esporta.
4.  Dall'elenco dei risultati, aggiungere un modulo di *Esportare i dati* all'area di prova.
5.  Connettere l'output del modulo *Del modello di punteggio* l'input del modulo di *Esportare i dati* . 
6.  Nel riquadro Proprietà selezionare **Il Database di SQL Azure** nella casella di riepilogo di destinazione dati.
7.  Il **nome del server di Database**, **nome del Database**, **nome account utente**e **password dell'account utente Server** campi, immettere le informazioni appropriate per il database.
8.  Nel campo **elenco di colonne da salvare separati da virgola** digitare totalizza etichette.
9.  Nel **campo Nome tabella di dati**, digitare dbo. ScoredLabels. Se la tabella non esiste, viene creata quando viene eseguita la prova o si chiama il servizio web.
10. Nel campo **elenco di colonne datatable virgole** digitare ScoredLabels.

Quando si scrive un'applicazione che chiama il servizio web finale, può essere necessario specificare una query di input diversa o una tabella di destinazione in fase di esecuzione. Per configurare queste ingressi e uscite, è possibile utilizzare la caratteristica di parametri di servizio Web per impostare la proprietà *origine dati* di modulo *Importa dati* e la proprietà di destinazione dati modalità *Esporta dati* .  Per ulteriori informazioni sui parametri di servizio Web, vedere la [voce di parametri di servizio Web AzureML](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) Intelligence Cortana e computer risorse Blog.

Per configurare i parametri del servizio Web per la query di importazione e la tabella di destinazione:

1.  Nel riquadro proprietà per il modulo *Importa dati* fare clic sull'icona nella parte superiore destra del campo di **una query di Database** e selezionare **Imposta come parametro al servizio web**.
2.  Nel riquadro proprietà per il modulo di *Esportare i dati* , fare clic sull'icona nella parte superiore destra del campo **nome tabella dati** e selezionare **Imposta come parametro al servizio web**.
3.  Nella parte inferiore del riquadro Proprietà modulo *Esporta dati* , nella sezione **Parametri di servizio Web** , fare clic su una query di Database e rinominare Query.
4.  Fare clic su **nome tabella dati** e rinominare **tabella**.

Al termine, la prova dovrebbe essere simile nella figura seguente.
 
![Aspetto finale di prova.](./media/machine-learning-web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

A questo punto è possibile distribuire la prova come un servizio web.

## <a name="deploy-the-web-service"></a>Distribuire il servizio web 
È possibile distribuire un classica o nuovo servizio web.

### <a name="deploy-a-classic-web-service"></a>Distribuire un servizio Web classico

Per distribuire come servizio Web classica e creare un'applicazione come utilizzarlo:

1.  Nella parte inferiore dell'area di prova, fare clic su Esegui.
2.  Al termine dell'esecuzione, fare clic su **Servizio Web di distribuzione** e selezionare **Distribuire il servizio Web [classica]**.
3.  Individuare la chiave dell'API nel dashboard di servizio web. Copiare e salvarla per utilizzarla in un secondo momento.
4.  Nella tabella **Endpoint predefinito** , fare clic sul collegamento **Esecuzione Batch** per aprire la pagina Guida API.
5.  In Visual Studio, creare un'applicazione console c#.
6.  Nella pagina Guida API individuare la sezione di **Codice di esempio** nella parte inferiore della pagina.
7.  Copiare e incollare il codice di esempio c# nel file Program.cs e rimuovere tutti i riferimenti a archiviazione blob.
8.  Aggiornare il valore della variabile *apiKey* con la chiave dell'API salvata in precedenza.
9.  Individuare la dichiarazione di richiesta e aggiornare i valori dei parametri del servizio Web che vengono passati ai moduli *Dati Importa* ed *Esporta dati* . In questo caso, utilizzare la query originale, si passerà definire un nuovo nome di tabella.

        var request = new BatchExecutionRequest() 
        {   
            GlobalParameters = new Dictionary<string, string>() {
            { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
            { "Table", "dbo.ScoredTable2" },
            }
        };

10. Eseguire l'applicazione. 

Al termine dell'esecuzione di una nuova tabella viene aggiunto al database contenente i risultati punteggio.

### <a name="deploy-a-new-web-service"></a>Distribuire un nuovo servizio Web

Per distribuire come un nuovo servizio Web e creare un'applicazione come utilizzarlo:

1.  Nella parte inferiore della prova area di lavoro, fare clic su **Esegui**.
2.  Al termine dell'esecuzione, fare clic su **Servizio Web di distribuzione** e selezionare **Distribuire [New] servizio Web**.
3.  Nella pagina distribuire prova immettere un nome per il servizio web e selezionare un piano a prezzo, quindi fare clic su **Distribuisci**.
4.  Nella pagina **Guida introduttiva** , fare clic su **consumo**.
5.  Nella sezione **Codice di esempio** fare clic su **Batch**.
6.  In Visual Studio, creare un'applicazione console c#.
7.  Copiare e incollare il codice di esempio c# nel file Program.cs.
8.  Aggiornare il valore della variabile *apiKey* con la **Chiave primaria** disponibile nella sezione **informazioni di base consumo** .
9.  Individuare la dichiarazione di *scoreRequest* e aggiornare i valori dei parametri del servizio Web che vengono passati ai moduli *Dati Importa* ed *Esporta dati* . In questo caso, utilizzare la query originale, si passerà definire un nuovo nome di tabella.

        var scoreRequest = new
        {
            Inputs = new Dictionary<string, StringTable>()
            {
            },
            GlobalParameters = new Dictionary<string, string>() {
                 { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable3" },
            }
        };

10. Eseguire l'applicazione. 
 

