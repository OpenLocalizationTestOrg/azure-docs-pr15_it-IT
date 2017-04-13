<properties
    pageTitle="Carico testare l'applicazione tramite Visual Studio Team Services | Microsoft Azure"
    description="Informazioni su come la verifica delle applicazioni di Azure servizio tessuti utilizzando Visual Studio Team Services."
    services="service-fabric"
    documentationCenter="na"
    authors="cawams"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="07/29/2016"
    ms.author="cawa" />

# <a name="load-test-your-application-by-using-visual-studio-team-services"></a>Carico testare l'applicazione tramite Visual Studio Team Services

In questo articolo viene illustrato come utilizzare le funzionalità di test carico di Microsoft Visual Studio per la verifica di un'applicazione. Utilizza un servizio informazioni sullo stato di Azure servizio tessuti back-end e un front-end senza informazioni sullo stato del servizio web. Nell'esempio di applicazione utilizzato in questo è un simulatore di posizione dell'aeroplano. Fornire un ID dell'aeroplano, orario di partenza e destinazione. Back-end dell'applicazione elabora le richieste e front-end consente di visualizzare su una mappa dell'aeroplano che soddisfa i criteri.

Il diagramma seguente illustra l'applicazione di servizio tessuti che è necessario testa.

![Diagramma dell'applicazione di posizione dell'aeroplano di esempio][0]

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, è necessario eseguire le operazioni seguenti:

- È possibile ottenere un account di Visual Studio Team Services. È possibile ottenerne uno gratuitamente in [Visual Studio Team Services](https://www.visualstudio.com).
- Ottenere e installare Visual Studio 2013 o Visual Studio 2015. In questo articolo utilizza Visual Studio 2015 Enterprise edition, ma Visual Studio 2013 e altre versioni dovrebbero funzionare in modo analogo.
- Distribuire l'applicazione in un ambiente di gestione temporanea. Per ulteriori informazioni, vedere [come distribuire le applicazioni a un cluster remoto tramite Visual Studio](service-fabric-publish-app-remote-cluster.md) .
- Comprendere il modello di utilizzo dell'applicazione. Queste informazioni vengono usate per simulare il modello di carico.
- Comprendere l'obiettivo per il test di carico. Consente di interpretare e analizzare i risultati.

## <a name="create-and-run-the-web-performance-and-load-test-project"></a>Creare ed eseguire il progetto delle prestazioni Web e Test di carico

### <a name="create-a-web-performance-and-load-test-project"></a>Creare un progetto delle prestazioni Web e Test di carico

1. Aprire Visual Studio 2015. Scegliere **File** > **Nuovo** > **progetto** sulla barra dei menu per aprire la finestra di dialogo **Nuovo progetto** .

2. Espandere il nodo **c#** e scegliere **Test** > **delle prestazioni Web e progetto di Test di carico**. Specificare un nome per il progetto e quindi scegliere **OK** .

    ![Schermata della finestra di dialogo Nuovo progetto][1]

    Verrà visualizzato un nuovo progetto delle prestazioni Web e Test di carico in Esplora soluzioni.

    ![Schermata della finestra di Esplora soluzioni che mostra il nuovo progetto][2]

### <a name="record-a-web-performance-test"></a>Registrare un test web

1. Aprire il progetto WebTest.

2. Fare clic sull'icona **Aggiungi registrazione** per avviare una sessione di registrazione nel browser.

    ![Schermata dell'icona Aggiungi registrazione in un browser][3]

    ![Schermata del pulsante di Record in un browser][4]

3. Passare all'applicazione di servizio tessuti. Il pannello di registrazione deve essere visualizzata richieste web.

    ![Schermata delle richieste web nel Pannello di registrazione][5]

4. Eseguire una sequenza di azioni che si sta aspettando agli utenti di eseguire. Queste azioni vengono utilizzate come modello per generare il carico.

5. Al termine, scegliere il pulsante **Interrompi** per interrompere la registrazione.

    ![Schermata del pulsante Interrompi][6]

    Progetto WebTest in Visual Studio deve rispettare una serie di richieste. Parametri dinamici vengono sostituiti automaticamente. A questo punto è possibile eliminare tutte le richieste di dipendenza aggiuntive, ripetuti che non fanno parte dello scenario di test.

6. Salvare il progetto e quindi scegliere il comando **Esegui Test** per eseguire il test web locale e assicurarsi che tutto funzioni correttamente.

    ![Schermata del comando Esegui Test][7]

### <a name="parameterize-the-web-performance-test"></a>Impostare i parametri del test web

È possibile aggiungere un parametro il test web la conversione in un test web codificato e quindi modificando il codice. In alternativa, è possibile associare il test web a un elenco di dati in modo che il test scorre i dati. Per informazioni dettagliate su come convertire il test web in un test codificato, vedere [Genera ed eseguire un test web codificato](https://msdn.microsoft.com/library/ms182552.aspx) . Per informazioni su come eseguire l'associazione dati a un test web, vedere [aggiungere un'origine dati da delle prestazioni web test](https://msdn.microsoft.com/library/ms243142.aspx) .

In questo esempio, si verrà convertire il test web in un test codificato in modo che è possibile sostituire l'ID dell'aeroplano con un GUID generato e aggiungere ulteriori richieste per inviare voli in posizioni diverse.

### <a name="create-a-load-test-project"></a>Creare un progetto di test di carico

Un progetto di test di carico è composta da uno o più scenari indicati i test delle prestazioni web e testare, insieme a impostazioni di test di carico specificato aggiuntive. La procedura seguente viene illustrato come creare un progetto di test di carico:

1. Dal menu di scelta rapida del progetto delle prestazioni Web e Test di carico, scegliere **Aggiungi** > **Test di carico**. Nella creazione guidata **Test di carico** , scegliere il pulsante **Avanti** per configurare le impostazioni di test.

2. Nella sezione **Modello di carico** scegliere se si desidera un carico costante utente o un carico passaggio che inizia con pochi utenti e aumenta gli utenti nel tempo.

    Se si ha una buona stima la quantità di carico utente e si desidera visualizzare le prestazioni del sistema corrente, scegliere **Carica costante**. Se si desidera scoprire se viene eseguita in modo coerente carichi diversi, scegliere **Carico per passaggio**.

3. Nella sezione **Combinazione di Test** , fare clic sul pulsante **Aggiungi** e quindi selezionare il test che si desidera includere nel test di carico. Per specificare la percentuale totale test per ogni test, è possibile utilizzare la colonna di **distribuzione** .

4. Nella sezione **Impostazioni di esecuzione** specificare la durata del test di carico.

    >[AZURE.NOTE] L'opzione di **Iterazioni Test** è disponibile solo quando si esegue un test di carico localmente utilizzando Visual Studio.

5. Nella sezione **posizione** delle **Impostazioni di esecuzione**, specificare la posizione in cui vengono generate richieste di test di carico. La procedura guidata potrebbe richiedere agli utenti di accedere al proprio account Team Services. Accedere e quindi scegliere una posizione geografica. Al termine, scegliere il pulsante **Fine** .

6. Una volta creato il test di carico, aprire il progetto LoadTest e scegliere esecuzione impostazione, ad esempio **Impostazioni di esecuzione**corrente > **Run Settings1 [Active]**. Le impostazioni di esecuzione verrà aperta nella finestra delle **proprietà** .

7. Nella sezione **risultati** della finestra delle proprietà **Impostazioni di esecuzione** , l'impostazione di **Intervallo archiviazione dettagli** deve avere **Nessuno** come valore predefinito. Impostare questo valore su **Tutti i singoli dettagli** per ottenere ulteriori informazioni sul carico i risultati dei test. Per ulteriori informazioni su come connettersi a Visual Studio Team Services ed eseguire un test di carico, vedere [Caricare test](https://www.visualstudio.com/load-testing.aspx) .

### <a name="run-the-load-test-by-using-visual-studio-team-services"></a>Eseguire il test di carico utilizzando Visual Studio Team Services

Scegliere il comando **Esegui Test carico** per avviare l'esecuzione del test.

![Schermata del comando eseguire Test di carico][8]

## <a name="view-and-analyze-the-load-test-results"></a>Visualizzare e analizzare i risultati

Come il caricamento di test avanza, rappresentate in un grafico le informazioni sulle prestazioni. Verrà visualizzata sarà simile nel grafico seguente.

![Schermata del grafico delle prestazioni per risultati][9]

1. Selezionare il collegamento **Scarica report** nella parte superiore della pagina. Dopo avere scaricato il report, fare clic sul pulsante **Visualizza report** .

    Nella scheda **grafico** è possibile visualizzare grafici per diversi contatori. Nella scheda **Riepilogo** vengono visualizzati i risultati dei test complessiva. Scheda **tabelle** Mostra il numero totale di test di carico passato e non riuscito.

2. Scegliere i collegamenti numerici del **Test** > **non riuscito** ed **errori** > **numero di** colonne per visualizzare i dettagli di errore.

    La scheda **Dettagli** Mostra virtuale test scenario le informazioni utente e le richieste non riuscite. Questi dati possono essere utili se il test di carico include più scenari.

Per ulteriori informazioni sulla visualizzazione di risultati, vedere [Analisi dei risultati Test di carico nella visualizzazione grafici dell'analizzatore Test di carico](https://www.visualstudio.com/load-testing.aspx) .

## <a name="automate-your-load-test"></a>Automatizzare il test di carico

Visual Studio Team servizi caricare Test fornisce API che consentono di gestire i test di carico e analizzare i risultati in un account di Team Services. Per ulteriori informazioni, vedere [API Rest test carico di Cloud](http://blogs.msdn.com/b/visualstudioalm/archive/2014/11/03/cloud-load-testing-rest-apis-are-here.aspx) .

## <a name="next-steps"></a>Passaggi successivi
- [Monitoraggio e la diagnosi servizi in una configurazione di sviluppo di computer locale](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[0]: ./media/service-fabric-vso-load-test/OverviewDiagram.png
[1]: ./media/service-fabric-vso-load-test/NewProjectDialog.png
[2]: ./media/service-fabric-vso-load-test/Project.png
[3]: ./media/service-fabric-vso-load-test/AddRecording.png
[4]: ./media/service-fabric-vso-load-test/AddRecording2.png
[5]: ./media/service-fabric-vso-load-test/ActionSequence.png
[6]: ./media/service-fabric-vso-load-test/StopRecording.png
[7]: ./media/service-fabric-vso-load-test/RunTest.png
[8]: ./media/service-fabric-vso-load-test/RunTest2.png
[9]: ./media/service-fabric-vso-load-test/Graph.png
