<properties
    pageTitle="Tenere traccia del flusso di un'applicazione di servizi Cloud con Azure diagnostica | Microsoft Azure"
    description="Aggiungere i messaggi di analisi a un'applicazione Azure per aiutare il debug, la misurazione delle prestazioni, monitoraggio, analisi del traffico e altro."
    services="cloud-services"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="02/20/2016"
    ms.author="robb"/>



# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Tenere traccia del flusso di un'applicazione di servizi Cloud con Azure diagnostica

La traccia è un modo per monitorare l'esecuzione dell'applicazione mentre è in esecuzione. È possibile utilizzare le classi [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx)e [System.Diagnostics.Debug](https://msdn.microsoft.com/library/system.diagnostics.debug.aspx) [System.Diagnostics.TraceSource](https://msdn.microsoft.com/library/system.diagnostics.tracesource.aspx) per registrare informazioni sugli errori ed esecuzione dell'applicazione in log, file di testo o altri dispositivi per l'analisi successiva. Per ulteriori informazioni sull'analisi, vedere [analisi e strumentazione di applicazioni](https://msdn.microsoft.com/library/zs6s4h68.aspx).


## <a name="use-trace-statements-and-trace-switches"></a>Utilizzare le istruzioni di analisi e opzioni di analisi

Implementare la traccia in un'applicazione di servizi Cloud aggiungendo [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) per la configurazione dell'applicazione e le chiamate a System.Diagnostics.Trace o System.Diagnostics.Debug nel codice dell'applicazione. Usare i file di configurazione *App* per i ruoli di lavoro e *config* per i ruoli di web. Quando si crea un nuovo servizio di hosting utilizzando un modello di Visual Studio, diagnostica Azure vengono aggiunti automaticamente al progetto e il DiagnosticMonitorTraceListener viene aggiunto al file di configurazione appropriato per i ruoli che si aggiungono.

Per informazioni sul posizionamento di traccia istruzioni, vedere [come: aggiungere le istruzioni di analisi al codice dell'applicazione](https://msdn.microsoft.com/library/zd83saa2.aspx).

Posizionando [Le opzioni di analisi](https://msdn.microsoft.com/library/3at424ac.aspx) nel codice, è possibile controllare se l'analisi avviene e come sia approfondita. Consente di controllare lo stato dell'applicazione in un ambiente di produzione. Questa operazione è particolarmente importante in un'applicazione aziendale che utilizza più componenti in esecuzione in più computer. Per ulteriori informazioni, vedere [How to: configurare le opzioni di analisi](https://msdn.microsoft.com/library/t06xyy08.aspx).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Configurare il listener in un'applicazione di Azure

Traccia di Debug e TraceSource, è necessario impostare "listener" per raccogliere e registrare i messaggi inviati. Listener raccolgono, archiviare e inviare messaggi di analisi. Indicano l'output di analisi a una destinazione appropriata, ad esempio un registro, una finestra o un file di testo. Diagnostica Windows Azure viene utilizzata la classe [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) .

Prima di completare la procedura seguente, è necessario inizializzare il monitor diagnostico Azure. Per eseguire questa operazione, vedere [Attivazione di diagnostica in Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Si noti che se si usano i modelli vengono forniti da Visual Studio, la configurazione di comunicare ascoltatore di viene aggiunto automaticamente.


### <a name="add-a-trace-listener"></a>Aggiungere un listener di analisi

1. Aprire il file config o App per il proprio ruolo.
2. Aggiungere il codice seguente al file. Modificare l'attributo di versione per utilizzare il numero di versione dell'assembly che si fa riferimento. La versione non è necessario intervenire con ogni versione Azure SDK a meno che non sono disponibili gli aggiornamenti.

    ```
    <system.diagnostics>
        <trace>
            <listeners>
                <add type="Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener,
                  Microsoft.WindowsAzure.Diagnostics,
                  Version=2.8.0.0,
                  Culture=neutral,
                  PublicKeyToken=31bf3856ad364e35"
                  name="AzureDiagnostics">
                  <filter type="" />
                </add>
            </listeners>
        </trace>
    </system.diagnostics>
    ```
    >[AZURE.IMPORTANT] Assicurarsi di avere un riferimento progetto assembly Microsoft.WindowsAzure.Diagnostics. Aggiornare il numero di versione nel xml sopra per corrisponde alla versione dell'assembly Microsoft.WindowsAzure.Diagnostics.

3. Salvare il file di configurazione.

Per ulteriori informazioni sui listener, vedere [Listener](https://msdn.microsoft.com/library/4y5y10s7.aspx).

Dopo aver completato la procedura per aggiungere comunicare ascoltatore, è possibile aggiungere le istruzioni di analisi al codice.


### <a name="to-add-trace-statement-to-your-code"></a>Per aggiungere al codice istruzioni di analisi

1. Aprire un file di origine per l'applicazione. Ad esempio, il <RoleName>cs per il ruolo di lavoro o web.
2. Aggiungere la seguente istruzione se non è già stato aggiunto:
    ```
        using System.Diagnostics;
    ```
3. Aggiungere le istruzioni di analisi in cui si desidera acquisire informazioni sullo stato dell'applicazione. È possibile utilizzare diversi metodi per formattare l'output dell'istruzione traccia. Per ulteriori informazioni, vedere [come: aggiungere le istruzioni di analisi al codice dell'applicazione](https://msdn.microsoft.com/library/zd83saa2.aspx).
4. Salvare il file di origine.
