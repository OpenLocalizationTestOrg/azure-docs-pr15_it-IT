<properties
    pageTitle="Come usare diagnostica Windows Azure in macchine virtuali | Microsoft Azure"
    description="Utilizzo di diagnostica Windows Azure per raccogliere dati da macchine virtuali di Microsoft Azure per il debug, la misurazione delle prestazioni, monitoraggio, analisi del traffico e altro."
    services="virtual-machines"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="virtual-machines"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="02/20/2016"
    ms.author="robb"/>



# <a name="enabling-diagnostics-in-azure-virtual-machines"></a>Attivazione della diagnostica in macchine virtuali di Azure

Per uno sfondo sulla diagnostica di Azure, vedere [Panoramica di diagnostica Azure](azure-diagnostics.md) .

## <a name="how-to-enable-diagnostics-in-a-virtual-machine"></a>Come abilitare diagnostica in un computer virtuale

Questa panoramica sulle procedure viene descritto come installazione remota di diagnostica per una macchina virtuale Azure da un computer di sviluppo. Anche informazioni su come implementare un'applicazione che viene eseguito sul computer virtuale Azure e genera i dati di telemetria utilizzando la [Classe EventSource][]. Diagnostica Windows Azure viene utilizzata per raccogliere le telemetria e archiviarlo in un account di archiviazione Azure.

### <a name="pre-requisites"></a>Prerequisiti in corso
Questa panoramica sulle procedure presuppone che si ha un abbonamento a Azure e Usa Visual Studio 2013 con Azure SDK. Se non si dispone di un abbonamento a Azure, è possibile iscriversi per la [Versione di valutazione gratuita][]. Assicurarsi di [installare e configurare Azure PowerShell versione 0.8.7 o versione successiva][].

### <a name="step-1-create-a-virtual-machine"></a>Passaggio 1: Creare una macchina virtuale
1.  Nel computer di sviluppo, avviare Visual Studio 2013.
2.  In Visual Studio **Esplora Server** espandere **Azure**, rapida **macchine virtuali** , quindi selezionare **Crea macchina virtuale**.
3.  Selezionare l'abbonamento Azure nella finestra di dialogo **Scegli un abbonamento** e fare clic su **Avanti**.
4.  Selezionare **Windows Server 2012 R2 Data Center, novembre 2014** nella finestra di dialogo **Seleziona immagine macchina virtuale** e fare clic su **Avanti**.
5.  Nelle **Impostazioni di base macchina virtuale**, impostare il nome del computer virtuale per "wadexample". Impostare il nome utente e la password e fare clic su **Avanti**.
6.  Nella finestra di dialogo **Impostazioni dei servizi Cloud** creare un nuovo servizio cloud denominato "wadexampleVM". Creare un nuovo account di archiviazione denominato "wadexample" e fare clic su **Avanti**.
7.  Fare clic su **Crea**.

### <a name="step-2-create-your-application"></a>Passaggio 2: Creare un'applicazione
1.  Nel computer di sviluppo, avviare Visual Studio 2013.
2.  Creare una nuova applicazione Visual c# Console destinato a .NET Framework 4.5. Nome del progetto "WadExampleVM".
    ![CloudServices_diag_new_project](./media/virtual-machines-dotnet-diagnostics/NewProject.png)
3.  Sostituire il contenuto di Program.cs con il codice riportato di seguito. La classe **SampleEventSourceWriter** implementa quattro metodi di registrazione: **SendEnums**, **MessageMethod**, **SetOther** e **HighFreq**. Il primo parametro per il metodo WriteEvent definisce l'ID per l'evento corrispondente. Il metodo di esecuzione di implementare un ciclo infinito che chiama ognuno dei metodi di registrazione implementati nella classe **SampleEventSourceWriter** ogni 10 secondi.

        using System;
        using System.Diagnostics;
        using System.Diagnostics.Tracing;
        using System.Threading;

        namespace WadExampleVM
        {
        sealed class SampleEventSourceWriter : EventSource
        {
            public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
            public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
            public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
            public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
            public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

        }

        enum MyColor
        {
            Red,
            Blue,
            Green
        }

        [Flags]
        enum MyFlags
        {
            Flag1 = 1,
            Flag2 = 2,
            Flag3 = 4
        }

        class Program
        {
        static void Main(string[] args)
        {
            Trace.TraceInformation("My application entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }

        }
        }
        }


4.  Salvare il file e scegliere **Compila soluzione** dal menu **Genera** per generare il codice.


### <a name="step-3-deploy-your-application"></a>Passaggio 3: Distribuire l'applicazione
1.  Pulsante destro del mouse sul progetto **WadExampleVM** in **Esplora soluzioni** e scegliere **Apri cartella in Esplora File**.
2.  Passare alla cartella *bin\Debug* e copiare tutti i file (WadExampleVM.*)
3.  In **Esplora Server** pulsante destro del mouse sul computer virtuale e scegliere **Connetti tramite Desktop remoto**.
4.  Una volta connessa la macchina virtuale creare una cartella denominata WadExampleVM e incollare l'applicazione di file nella cartella.
5.  Avviare l'applicazione WadExampleVM.exe. Verrà visualizzata una finestra console vuota.

### <a name="step-4-create-your-diagnostics-configuration-and-install-the-extension"></a>Passaggio 4: Creare la configurazione di diagnostica e installare l'estensione
1.  Scaricare la definizione dello schema di configurazione pubblico file nel computer di sviluppo eseguendo il seguente comando di PowerShell:

        (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'

2.  Aprire un nuovo file XML in Visual Studio, in un progetto è già aperto o in Visual Studio istanza con senza progetti aperti. In Visual Studio, selezionare **Aggiungi** -> **Nuovo elemento...**  ->  **Elementi visual c#** -> **dati** -> **File XML**. Assegnare un nome file "WadExample.xml"
3.  Associare il WadConfig.xsd il file di configurazione. Verificare che la finestra editor WadExample.xml sia la finestra attiva. Premere **F4** per aprire la finestra **proprietà** . Fare clic su proprietà **schemi** nella finestra delle **proprietà** . Fare clic su **…** in proprietà **schemi** . Scegliere il **pulsante Aggiungi....** pulsante e passare al percorso in cui è salvato il file XSD e selezionare il file WadConfig.xsd. Fare clic su **OK**.
4.  Sostituire il contenuto del file di configurazione WadExample.xml con il seguente codice XML e salvare il file. In questo file di configurazione definisce un paio contatori delle prestazioni per raccogliere: uno per l'utilizzo della CPU e uno per l'utilizzo della memoria. La configurazione definisce quindi quattro eventi corrispondenti ai metodi di classe SampleEventSourceWriter.

```
        <?xml version="1.0" encoding="utf-8"?>
        <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
            <WadCfg>
                <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
                <PerformanceCounters scheduledTransferPeriod="PT1M">
                    <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
                    <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
                    </PerformanceCounters>
                    <EtwProviders>
                        <EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
                            <Event id="1" eventDestination="EnumsTable"/>
                            <Event id="2" eventDestination="MessageTable"/>
                            <Event id="3" eventDestination="SetOtherTable"/>
                            <Event id="4" eventDestination="HighFreqTable"/>
                            <DefaultEvents eventDestination="DefaultTable" />
                        </EtwEventSourceProviderConfiguration>
                    </EtwProviders>
                </DiagnosticMonitorConfiguration>
            </WadCfg>
        </PublicConfig>
```

### <a name="step-5-remotely-install-diagnostics-on-your-azure-virtual-machine"></a>Passaggio 5: Installazione remota di diagnostica la macchina virtuale Azure
I cmdlet di PowerShell per la gestione di diagnostica su una macchina virtuale sono: Set-AzureVMDiagnosticsExtension, Get-AzureVMDiagnosticsExtension e AzureVMDiagnosticsExtension Rimuovi.

1.  Nel computer di sviluppo, aprire Azure PowerShell.
2.  Eseguire lo script per installare diagnostica sul Virtual Machine (Sostituisci *StorageAccountKey* con la chiave account lo spazio di archiviazione per l'account di archiviazione wadexamplevm):

        $storage_name = "wadexamplevm"
        $key = "<StorageAccountKey>"
        $config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExampleVM\WadExampleVM\WadExample.xml"
        $service_name="wadexamplevm"
        $vm_name="WadExample"
        $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
        $VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name
        $VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext
        $VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM


### <a name="step-6-look-at-your-telemetry-data"></a>Passaggio 6: Visualizzare i dati di telemetria
In Visual Studio **Esplora Server** passare all'account di archiviazione wadexample. Dopo la macchina virtuale è in esecuzione circa 5 minuti risulta tabelle **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** e **WADSetOtherTable**. Fare doppio clic su una delle tabelle per visualizzare telemetria che è stati raccolti.

![CloudServices_diag_wadexamplevm_tables](./media/virtual-machines-dotnet-diagnostics/WadExampleVMTables.png)

## <a name="configuration-file-schema"></a>Schema di file di configurazione

Il file di configurazione diagnostica definisce valori che vengono utilizzati per inizializzare le impostazioni di configurazione di diagnostica all'avvio di agente di diagnostica. Vedere il [riferimento allo schema più recente](https://msdn.microsoft.com/library/azure/mt634524.aspx) per i valori validi ed esempi.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Per ulteriori informazioni, vedere [Risoluzione dei problemi di diagnostica Azure](azure-diagnostics-troubleshooting.md) .


## <a name="next-steps"></a>Passaggi successivi
[Diagnostica Windows Azure articoli relativi a visualizzare un elenco di macchina virtuale](azure-diagnostics.md#virtual-machines-using-azure-diagnostics) per modificare i dati raccolti, risolvere i problemi o altre informazioni di diagnostica in generale.


[EventSource classe]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Versione di valutazione gratuita]: http://azure.microsoft.com/pricing/free-trial/
[Installare e configurare Azure PowerShell versione 0.8.7 o versioni successive]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/
