<properties 
    pageTitle="Analisi di un servizio Cloud localmente nell'emulatore calcolo | Microsoft Azure" 
    services="cloud-services"
    description="Esaminare i problemi di prestazioni nei servizi cloud con Visual Studio profiler" 
    documentationCenter=""
    authors="TomArcher" 
    manager="douge" 
    editor=""
    tags="" 
    />

<tags 
    ms.service="cloud-services" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="07/30/2016" 
    ms.author="tarcher"/>

# <a name="testing-the-performance-of-a-cloud-service-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Test le prestazioni di un servizio Cloud localmente nell'emulatore calcolo Azure utilizzando il Profiler di Visual Studio

Diversi strumenti e tecniche sono disponibili per la verifica delle prestazioni di servizi cloud.
Quando si pubblica un servizio cloud di Azure, è possibile utilizzare Visual Studio raccogliere dati di analisi e quindi analizzarli in locale, come descritto nella [analisi di un'applicazione di Azure][1].
È anche possibile utilizzare diagnostica per tenere traccia di una varietà di contatori delle prestazioni, come descritto in [uso contatori in Azure][2].
È anche consigliabile di analisi dell'applicazione localmente nell'emulatore di calcolo prima di distribuirlo nel cloud.

Questo articolo descrive il metodo di CPU campioni di analisi, che può essere eseguito localmente nell'emulatore. Esempio CPU è un metodo che non è molto intrusivo. In un intervallo di campioni designato profiler accetta uno snapshot dello stack. I dati raccolti per un periodo di tempo, visualizzati in un report. Questo metodo viene per indicare dove un'applicazione di calcoli in modo intensivo la maggior parte del lavoro CPU viene eseguito.  Si ottiene così l'opportunità per concentrarsi su "percorso critico" nel punto in cui l'applicazione è impiegare più tempo.



## <a name="1-configure-visual-studio-for-profiling"></a>1: configurare Visual Studio per l'analisi

Prima di tutto, sono disponibili alcune opzioni di configurazione di Visual Studio che potrebbero risultare utili durante l'analisi. Per organizzare meglio i report di analisi, è necessario simboli (PDB) per l'applicazione e anche i simboli per le raccolte di sistema. È consigliabile per assicurarsi di fare riferimento al server dei simboli disponibili. A tale scopo, nel menu **Strumenti** in Visual Studio, scegliere **Opzioni**, quindi scegliere **debug**, quindi **simboli**. Assicurarsi che il server di simboli Microsoft è elencato con **percorsi dei file (PDB) simboli**.  È anche possibile fare riferimento http://referencesource.microsoft.com/symbols, che può essere file di altri simboli.

![Opzioni simbolo][4]

Se lo si desidera, è possibile semplificare i report che genera il profiler mediante l'impostazione Just My Code. Con Just My Code attivata, stack funzione vengono semplificate in modo che le chiamate interamente interne per le raccolte e .NET Framework sono nascoste ai report. Scegliere **Opzioni**dal menu **Strumenti** . Espandere il nodo **Strumenti di prestazioni** , quindi scegliere **Generale**. Selezionare la casella di controllo per **Attivare Just My Code per i report del profiler**.

![Solo le opzioni di codice personale][17]

È possibile utilizzare queste istruzioni con un progetto esistente o con un nuovo progetto.  Se si crea un nuovo progetto per provare le procedure descritte di seguito, scegliere un progetto c# **Servizio Cloud di Azure** e selezionare un **Ruolo Web** e un **Ruolo di lavoro**.

![Ruoli di progetto servizio Cloud Azure][5]

Scopi, ad esempio aggiungere codice al progetto che richiede molto tempo e illustra un problema di prestazioni più evidenti. Ad esempio, aggiungere il codice seguente a un progetto di ruolo di lavoro:

    public class Concatenator
    {
        public static string Concatenate(int number)
        {
            int count;
            string s = "";
            for (count = 0; count < number; count++)
            {
                s += "\n" + count.ToString();
            }
            return s;
        }
    }

Chiamare il codice dal metodo RunAsync in classe derivato RoleEntryPoint del ruolo di lavoro. (Ignorare l'avviso sul metodo di esecuzione sincrona).

        private async Task RunAsync(CancellationToken cancellationToken)
        {
            // TODO: Replace the following with your own logic.
            while (!cancellationToken.IsCancellationRequested)
            {
                Trace.TraceInformation("Working");
                Concatenator.Concatenate(10000);
            }
        }

Creare ed eseguire il servizio cloud localmente senza eseguire il debug (Ctrl + F5), con la configurazione della soluzione impostata su **Release**. In questo modo che tutti i file e cartelle vengono create per l'esecuzione dell'applicazione in locale e assicura che tutti gli emulatori vengono avviati. Avviare l'interfaccia utente emulatore calcolare dalla barra delle applicazioni per verificare che il proprio ruolo di lavoro è in esecuzione.

## <a name="2-attach-to-a-process"></a>2: connettersi a un processo

Invece di analisi di un'applicazione a partire da IDE di Visual Studio 2010, è necessario connettere il profiler processi in esecuzione. 

Per connettere il profiler a un processo, nel menu **Analizza** scegliere **Profiler** e **Connetti/Disconnetti**.

![Allegare opzione profilo][6]

Per un ruolo di lavoro, individuare il processo di WaWorkerHost.exe.

![Processo WaWorkerHost][7]

Se la cartella di progetto è un'unità di rete, il profiler chiederà di specificare un altro percorso in cui salvare il report di analisi.

 È anche possibile allegare a un ruolo web tramite il collegamento a WaIISHost.exe.
Se sono presenti più processi di ruolo di lavoro nell'applicazione, è necessario usare l'ID per distinguerle. È possibile richiedere l'ID a livello di programmazione accedendo all'oggetto processo. Ad esempio, se si aggiunge il codice per il metodo Run della classe derivata RoleEntryPoint in un ruolo, possibile esaminare il log dell'interfaccia utente emulatore calcolare sapere quali processo a cui connettersi.

    var process = System.Diagnostics.Process.GetCurrentProcess();
    var message = String.Format("Process ID: {0}", process.Id);
    Trace.WriteLine(message, "Information");

Per visualizzare il log, avviare l'interfaccia utente emulatore calcolare.

![Avviare l'emulatore di elaborazione dell'interfaccia utente][8]

Aprire la finestra di console lavoro ruolo log dell'interfaccia utente emulatore calcolare facendo clic sulla barra del titolo della finestra console. È possibile visualizzare l'ID di processo nel registro.

![ID del processo di visualizzazione][9]

Uno è stato allegato, eseguire i passaggi dell'interfaccia utente dell'applicazione (se necessario) per riprodurre lo scenario.

Quando si desidera interrompere il profilo, selezionare il collegamento **Interrompi analisi** .

![Interrompere l'opzione di analisi][10]

## <a name="3-view-performance-reports"></a>3: visualizzare report sulle prestazioni

Viene visualizzato il report di prestazioni dell'applicazione.

A questo punto, il profiler interrompe l'esecuzione, Salva i dati in un file vsp e consente di visualizzare un report che mostra un'analisi dei dati.

![Rapporto profiler][11]


Se viene visualizzato String.wstrcpy nel percorso critico, fare clic su Just My Code per modificare la visualizzazione per mostrare solo il codice utente.  Se viene visualizzato Concat, provare a premere il pulsante Mostra tutto il codice.

Verrà visualizzato il metodo concatena e Concat occupi gran parte del tempo di esecuzione.

![Analisi dei report][12]

Se è stato aggiunto il codice di concatenazione di stringhe in questo articolo, verrà visualizzato un avviso nell'elenco attività per l'oggetto. Si può anche visualizzato un avviso che c'è una quantità eccessiva di simultanea causa del numero di stringhe che vengono creati ed eliminato.

![Avvisi di prestazioni][14]

## <a name="4-make-changes-and-compare-performance"></a>4: apportare le modifiche e confrontare le prestazioni

È inoltre possibile confrontare le prestazioni prima e dopo una modifica al codice.  Interrompere il processo in esecuzione e modificare il codice per sostituire l'operazione di concatenazione di stringhe con l'utilizzo di StringBuilder:

    public static string Concatenate(int number)
    {
        int count;
        System.Text.StringBuilder builder = new System.Text.StringBuilder("");
        for (count = 0; count < number; count++)
        {
             builder.Append("\n" + count.ToString());
        }
        return builder.ToString();
    }

Eseguire un'altra prestazioni Esegui e quindi confrontare le prestazioni. In Esplora aree di prestazioni se le esecuzioni sono nella stessa sessione, è possibile solo selezionare entrambi i rapporti, aprire il menu di scelta rapida e scegliere **Confronta report di prestazioni**. Se si desidera confrontare con una sequenza di testo in un'altra sessione di prestazioni, aprire il menu **Analizza** e scegliere **Confronta report di prestazioni**. Specificare che entrambi i file nella finestra di dialogo visualizzata.

![Confrontare l'opzione di report di prestazioni][15]

I report di evidenziare le differenze tra le due esecuzioni.

![Report di confronto][16]

Congratulazioni! È stato avviato con il profiler.

## <a name="troubleshooting"></a>Risoluzione dei problemi

- Verificare che siano analisi build di rilascio e avviare senza eseguire il debug.

- Se l'opzione Connetti/Disconnetti non è attivato nel menu Profiler, eseguire la procedura guidata delle prestazioni.

- Utilizzare l'interfaccia utente emulatore calcolare per visualizzare lo stato dell'applicazione. 

- Se si hanno problemi di avvio di applicazioni nell'emulatore o connessione del profiler Chiudi verso il basso l'emulatore di calcolo e riavviarla. Se non viene risolto il problema, provare a riavviare. Questo problema può verificarsi se si utilizza l'emulatore di calcolo per sospendere e rimuovere distribuzioni in esecuzione.

- Se si utilizzano i comandi profilo dalla riga di comando, soprattutto delle impostazioni globali, assicurarsi che è stato chiamato VSPerfClrEnv /globaloff e che VsPerfMon.exe è stato chiuso.

- Se al sondaggio, viene visualizzato il messaggio "PRF0025: dati non raccolti," verificare che il processo si era connessi ha attività della CPU. Applicazioni che non eseguono operazioni calcolo potrebbero non produrre i dati di esempio.  È inoltre possibile che il processo è terminato prima che venisse eseguito qualsiasi campioni. Verificare che il metodo di esecuzione per un ruolo analisi non termina.

## <a name="next-steps"></a>Passaggi successivi

Strumentazione di Azure binari nell'emulatore non è supportata in profiler Visual Studio, ma se si desidera verificare l'allocazione della memoria, è possibile scegliere l'opzione durante l'analisi. È anche possibile scegliere di analisi della concorrenza, che consente di determinare se thread sono tempi competizione per blocchi o profilo interazione tra livelli, che consente di individuare i problemi di prestazioni durante l'interazione tra livelli di un'applicazione, più comunemente tra il livello di dati e un ruolo di lavoro.  È possibile visualizzare le query di database che genera l'app e usare i dati di analisi per migliorare l'utilizzo del database. Per informazioni su profilo interazione tra livelli, vedere il post del blog [procedura dettagliata: utilizzo del Profiler di interazione di livello in Visual Studio Team System 2010][3].



[1]: http://msdn.microsoft.com/library/azure/hh369930.aspx
[2]: http://msdn.microsoft.com/library/azure/hh411542.aspx
[3]: http://blogs.msdn.com/b/habibh/archive/2009/06/30/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010.aspx
[4]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally09.png
[5]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally10.png
[6]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally02.png
[7]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally05.png
[8]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally010.png
[9]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally07.png
[10]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally06.png
[11]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally03.png
[12]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally011.png
[14]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally04.png 
[15]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally013.png
[16]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally012.png
[17]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally08.png
 