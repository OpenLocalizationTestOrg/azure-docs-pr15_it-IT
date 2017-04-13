<properties
    pageTitle="Archiviare e visualizzare dati di diagnostica in archiviazione Azure | Microsoft Azure"
    description="Recuperare dati di diagnostica Windows Azure in archiviazione Azure e visualizzarlo"
    services="cloud-services"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor="tysonn" />
<tags
    ms.service="cloud-services"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/01/2016"
    ms.author="robb" />

# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Archiviare e visualizzare dati di diagnostica in archiviazione Azure

Dati di diagnostica non vengono archiviati in modo definitivo a meno che non si trasferire all'emulatore di spazio di archiviazione di Microsoft Azure o allo spazio di archiviazione Azure. Una volta nel sistema di archiviazione, si possono essere visualizzato con uno dei numerosi strumenti disponibili.

## <a name="specify-a-storage-account"></a>Specificare un account di archiviazione

Specificare l'account di archiviazione che si desidera utilizzare nel file ServiceConfiguration. cscfg. Le informazioni sull'account è definiti come una stringa di connessione in un'impostazione di configurazione. Nell'esempio seguente viene creata un nuovo progetto di servizio Cloud in Visual Studio la stringa di connessione predefinito:


```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

È possibile modificare questa stringa di connessione per fornire informazioni sull'account per un account di archiviazione Azure.

A seconda del tipo di dati di diagnostica che sono stati raccolti, diagnostica Azure utilizza il servizio Blob o il servizio di tabella. Nella tabella seguente mostra le origini dati che vengono mantenute e il relativo formato.

|Origine dati|Formato di archiviazione|
|---|---|
|Registri Azure|tavolo|
|Registri di IIS 7.0|BLOB|
|Azure registri dell'infrastruttura di diagnostica|tavolo|
|Non è possibile richiedere traccia dei registri|BLOB|
|Registri eventi di Windows|tavolo|
|Contatori delle prestazioni|tavolo|
|Anomalo|BLOB|
|Registri di errori personalizzato|BLOB|

## <a name="transfer-diagnostic-data"></a>Trasferire dati di diagnostica

Per SDK 2.5 e versioni successive, la richiesta di trasferimento dati di diagnostica può verificarsi tramite il file di configurazione. È possibile trasferire i dati di diagnostica intervalli pianificati come specificato nella configurazione.

Per SDK 2,4 precedente ed è possibile richiedere per trasferire i dati di diagnostici tramite il file di configurazione anche a livello di codice. Il livello di programmazione approccio consente anche di eseguire trasferimenti su richiesta.


>[AZURE.IMPORTANT] Quando si trasferiscono dati di diagnostica a un account di archiviazione Azure, si verifica i costi per le risorse di archiviazione che utilizza i dati di diagnostica.

## <a name="store-diagnostic-data"></a>Archiviare i dati di diagnostica

Registro dati vengono archiviati in archiviazione Blob o tabella con i nomi seguenti:

**Tabelle**

- **WadLogsTable** - log scritto in codice utilizzando comunicare ascoltatore traccia.

- **WADDiagnosticInfrastructureLogsTable** - monitor diagnostica e la configurazione viene modificato.

- **WADDirectoriesTable** -directory che esegue il monitoraggio monitor diagnostico.  Sono inclusi i registri di IIS, IIS non riuscita registri delle richieste e directory personalizzate.  Il percorso del file di log blob è specificato nel campo contenitore e il nome del blob nel campo RelativePath.  Il campo AbsolutePath indica il percorso e il nome del file come quella esistente nel computer virtuale Azure.

- **WADPerformanceCountersTable** -contatori delle prestazioni.

- **WADWindowsEventLogsTable** : i registri eventi di Windows.

**BLOB**

- **contenitore di controlli tampone** -(solo per 2,4 SDK e precedente) contiene i file di configurazione XML che controlla diagnostica Windows Azure.

- **failedreqlogfiles di iis tampone** – registra contiene le informazioni da IIS non è possibile richiedere.

- **registri di iis tampone** – contiene le informazioni relative a IIS registra.

- **"personalizzato"** : un contenitore personalizzato in base a configurazione di directory vengono monitorate dal monitor diagnostico.  Il nome del contenitore blob verrà specificato in WADDirectoriesTable.

## <a name="tools-to-view-diagnostic-data"></a>Strumenti per visualizzare dati di diagnostica
Sono disponibili per visualizzare i dati dopo il trasferimento allo spazio di archiviazione diversi strumenti. Per esempio:

- Esplora server in Visual Studio - se sono stati installati gli strumenti di Azure per Microsoft Visual Studio, è possibile utilizzare il nodo dello spazio di archiviazione di Azure in Esplora Server per visualizzare dagli account Azure dello spazio di archiviazione blob di sola lettura e dati di una tabella. È possibile visualizzare i dati dall'account emulatore archivio locale e anche dall'account di archiviazione sono stati creati per Azure. Per ulteriori informazioni, vedere [esplorazione e la gestione risorse di archiviazione con Esplora risorse Server](../vs-azure-tools-storage-resources-server-explorer-browse-manage.md).

- [Esplora archivi di Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) è un'applicazione autonoma che consente di utilizzare facilmente i dati di archiviazione Azure in Windows, OSX e Linux.

- [Azure Management Studio](http://www.cerebrata.com/products/azure-management-studio/introduction) include Azure Diagnostics Manager che consente di visualizzare, scaricare e gestire i dati di diagnostica raccolti dalle applicazioni in esecuzione in Azure.


## <a name="next-steps"></a>Passaggi successivi

[Tenere traccia del flusso in un'applicazione di servizi Cloud con Azure diagnostica](cloud-services-dotnet-diagnostics-trace-flow.md)
