<properties
   pageTitle="Il debug dell'applicazione in Visual Studio | Microsoft Azure"
   description="Migliorare l'affidabilità e le prestazioni dei servizi da sviluppo e il debug in Visual Studio in un cluster di sviluppo locale."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/21/2016"
   ms.author="vturecek;mikhegn"/>

# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Il debug dell'applicazione di servizio tessuti utilizzando Visual Studio

## <a name="debug-a-local-service-fabric-application"></a>Eseguire il debug di un'applicazione di servizio tessuti locale

È possibile risparmiare tempo e denaro per la distribuzione e il debug dell'applicazione di Azure servizio tessuti in un cluster di sviluppo di computer locale. Visual Studio possibile distribuire un'applicazione di cluster locale e connettere automaticamente verrà a tutte le istanze dell'applicazione.

1. Avviare un cluster di sviluppo locale seguendo i passaggi di [configurazione dell'ambiente di sviluppo di servizio tessuti](service-fabric-get-started.md).

2. Premere **F5** o fare clic su **Debug** > **Avvia debug**.

    ![Avviare il debug di un'applicazione][startdebugging]

3. Impostare i punti di interruzione nel codice e passaggio tramite l'applicazione tramite i comandi nel menu **Debug** .

    > [AZURE.NOTE] Visual Studio viene associato a tutte le istanze dell'applicazione. Mentre ci si sta allontanando tramite il codice, i punti di interruzione potrebbe raggiunto da più processi risultante sessioni simultanee. Provare a disattivare i punti di interruzione dopo che è raggiunto mediante effettua ogni punto di interruzione condizionale su ID thread o eventi di diagnostica.

4. La finestra di **Eventi di diagnostica** verrà aperta automaticamente in modo da visualizzare eventi di diagnostica in tempo reale.

    ![Visualizzare gli eventi di diagnostica in tempo reale][diagnosticevents]

5. È anche possibile aprire la finestra di **Eventi di diagnostica** nel Cloud Explorer.  In **Servizio tessuti**rapida qualsiasi nodo e scegliere **Visualizzazione Streaming tracce**.

    ![Aprire la finestra di eventi di diagnostica][viewdiagnosticevents]

    Se si desidera filtrare le tracce in un'applicazione o servizio specifico, è sufficiente abilitare tracce flusso su tale servizio specifico o dell'applicazione.

6. Gli eventi di diagnostica possono essere visualizzati il file **ServiceEventSource.cs** generato automaticamente e chiamati dal codice dell'applicazione.

    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```

7. La finestra di **Eventi di diagnostica** supporta il filtro, pausa e verifica degli eventi in tempo reale.  Il filtro è una semplice stringa di ricerca del messaggio dell'evento, incluso il relativo contenuto.

    ![Filtrare, posizionare il puntatore e riprendere o esaminare gli eventi in tempo reale][diagnosticeventsactions]

8. Servizi di debug sono simile al debug di qualsiasi altra applicazione. Impostare normalmente il interruzione tramite Visual Studio per il debug semplice. Anche se le raccolte affidabile replicare in più nodi, vengono comunque implementare IEnumerable. Ciò significa che è possibile utilizzare la visualizzazione dei risultati in Visual Studio durante il debug per vedere che cos'è memorizzato all'interno. È sufficiente impostare un'interruzione in un punto qualsiasi nel codice.

    ![Avviare il debug di un'applicazione][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Eseguire il debug di un'applicazione di servizio tessuti remota

Se le applicazioni di servizio tessuti sono in esecuzione in un cluster di servizio tessuti in Azure, ora possibile eseguire il debug remoto questi, direttamente da Visual Studio.

> [AZURE.NOTE] La caratteristica richiede [Service tessuti SDK 2.0](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) e [Azure SDK per .NET 2.9](https://azure.microsoft.com/downloads/).    

<!-- -->
> [AZURE.WARNING] Il debug remoto è utilizzato per gli scenari di sviluppo/test e non deve essere utilizzato in ambienti di produzione, a causa l'impatto sulle applicazioni in esecuzione.

1. Passare al cluster in **Esplora Cloud**, pulsante destro del mouse e scegliere **Attiva debug**

    ![Attivare il debug remoto][enableremotedebugging]

    Questa operazione verrà calcio il processo di attivazione l'estensione di debug remoto su nodi cluster, nonché le configurazioni di rete necessari.

2. Il pulsante destro nodo cluster nello **Cloud Explorer**e scegliere **Allega Debugger**

    ![Connettere debugger][attachdebugger]

3. Nella finestra di dialogo **Connetti a processo** , scegliere il processo che si desidera eseguire il debug e fare clic su **Allega**

    ![Scegliere processo][chooseprocess]

    Il nome del processo che si desidera allegare, è uguale al nome del proprio nome di assembly di progetto di servizio.

    Verrà verrà collegato a tutti i nodi esecuzione del processo.
    - Nel caso in cui si esegue il debug di un servizio senza informazioni sullo stato, tutte le istanze del servizio in tutti i nodi fanno parte della sessione di debug.
    - Se il debug di un servizio informazioni sullo stato, la principale replica di ogni partizione sarà attiva e pertanto catturati da verrà. Se si sposta replica primaria durante la sessione di debug, l'elaborazione di tale replica saranno comunque parte della sessione di debug.
    - Per rilevare solo le partizioni pertinenti o istanze di un determinato servizio, è possibile utilizzare punti di interruzione condizionali per suddividere solo una partizione specifica o istanza.

    ![Punto di interruzione condizionale][conditionalbreakpoint]

    > [AZURE.NOTE] Attualmente non è supportato il debug di un cluster di servizio tessuti con più istanze con lo stesso nome eseguibile del servizio.

4. Dopo aver completato il debug dell'applicazione, è possibile disattivare l'estensione di debug remoto facendo clic con il cluster nel **Cloud Explorer** e scegliere **Disattiva debug**

    ![Disabilitare il debug remoto][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Trasmissione tracce da un nodo cluster remoto

Si è anche possibile alle tracce flusso direttamente da un nodo cluster remoto per Visual Studio. Questa caratteristica consente agli eventi di traccia ETW flusso, creati in un nodo cluster tessuti servizio, direttamente in Visual Studio.

> [AZURE.NOTE] La caratteristica richiede [Service tessuti SDK 2.0](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) e [Azure SDK per .NET 2.9](https://azure.microsoft.com/downloads/).

<!-- -->
> [AZURE.WARNING]Streaming tracce è utilizzata per gli scenari di sviluppo/test e non deve essere utilizzato in ambienti di produzione, a causa l'impatto sulle applicazioni in esecuzione.
> In uno scenario di produzione, è consigliabile basarsi su inoltro eventi mediante diagnostica Azure.

1. Passare al cluster in **Esplora Cloud**, pulsante destro del mouse e scegliere **Abilita tracce di flusso**

    ![Abilitare tracce flusso remote][enablestreamingtraces]

    Questa operazione verrà calcio il processo di attivazione l'estensione tracce flusso su nodi cluster, nonché le configurazioni di rete necessari.

2. Espandere l'elemento **nodi** nel **Cloud Explorer**destro del mouse su nodo che si desidera flusso tracce da e scegliere **Visualizzazione Streaming tracce**

    ![Visualizzazione remoto streaming tracce][viewremotestreamingtraces]

    Ripetere il passaggio 2 per qualsiasi numero di nodi che si desidera visualizzare le tracce da. Ogni flusso nodi verrà visualizzato in una finestra dedicata.

    Si è ora possibile visualizzare le tracce emesse dal servizio tessuti e i servizi. Se si desidera filtrare gli eventi per visualizzare solo una specifica applicazione, è sufficiente digitare il nome dell'applicazione del filtro.

    ![Visualizzazione flusso tracce][viewingstreamingtraces]

4. Dopo aver tracce flusso dal cluster, è possibile disattivare le tracce di trasmissione remote, facendo clic con il cluster nel **Cloud Explorer** e scegliere **Disattiva tracce di flusso**

    ![Disabilitare tracce flusso remote][disablestreamingtraces]

## <a name="next-steps"></a>Passaggi successivi

- [Test un servizio di assistenza tessuti](service-fabric-testability-overview.md).
- [Gestire le applicazioni di servizio tessuti in Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
[enableremotedebugging]: ./media/service-fabric-debugging-your-application/enableremotedebugging.png
[attachdebugger]: ./media/service-fabric-debugging-your-application/attachdebugger.png
[chooseprocess]: ./media/service-fabric-debugging-your-application/chooseprocess.png
[conditionalbreakpoint]: ./media/service-fabric-debugging-your-application/conditionalbreakpoint.png
[disableremotedebugging]: ./media/service-fabric-debugging-your-application/disableremotedebugging.png
[enablestreamingtraces]: ./media/service-fabric-debugging-your-application/enablestreamingtraces.png
[viewingstreamingtraces]: ./media/service-fabric-debugging-your-application/viewingstreamingtraces.png
[viewremotestreamingtraces]: ./media/service-fabric-debugging-your-application/viewremotestreamingtraces.png
[disablestreamingtraces]: ./media/service-fabric-debugging-your-application/disablestreamingtraces.png
