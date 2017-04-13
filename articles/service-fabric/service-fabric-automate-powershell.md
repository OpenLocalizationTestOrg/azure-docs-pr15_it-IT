<properties
    pageTitle="Automatizzare Gestione applicazioni di servizio tessuti tramite PowerShell | Microsoft Azure"
    description="Distribuire, eseguire l'aggiornamento, verificare e rimuovere le applicazioni di servizio tessuti tramite PowerShell."
    services="service-fabric"
    documentationCenter=".net"
    authors="rwike77"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-fabric"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/25/2016"
    ms.author="ryanwi"/>

# <a name="automate-the-application-lifecycle-using-powershell"></a>Automatizzare il ciclo di vita dell'applicazione tramite PowerShell

È possibile automatizzare molti aspetti del [ciclo di vita applicazione servizio tessuti](service-fabric-application-lifecycle.md) .  In questo articolo viene illustrato come usare PowerShell per automatizzare le attività comuni per la distribuzione, l'aggiornamento, rimozione e verifica delle applicazioni di Azure servizio tessuti.  Gestite e APIs HTTP per la gestione di app sono inoltre disponibili. [Ciclo di vita di app](service-fabric-application-lifecycle.md) per ulteriori informazioni, vedere.  

## <a name="prerequisites"></a>Prerequisiti
Prima di passare all'attività nell'articolo, assicurarsi di:

+ Acquisire familiarità con i concetti di servizio tessuti descritti nella [Panoramica tecnica di servizio tessuti](service-fabric-technical-overview.md).
+ [Installare il runtime, SDK e strumenti](service-fabric-get-started.md), che consente di installare il modulo di PowerShell **ServiceFabric** .
+ [Esecuzione di script di PowerShell abilitare](service-fabric-get-started.md#enable-powershell-script-execution).
+ Avviare un cluster locale.  Avviare una nuova finestra di PowerShell come amministratore e quindi eseguire lo script di installazione cluster dalla cartella SDK:`& "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"`
+ Prima di eseguire i comandi di PowerShell in questo articolo, connettersi al cluster tessuti servizio locale utilizzando [**Connetti ServiceFabricCluster**](https://msdn.microsoft.com/library/azure/mt125938.aspx):`Connect-ServiceFabricCluster localhost:19000`
+ Le attività seguenti richiedono un pacchetto dell'applicazione v1 per distribuire e un pacchetto dell'applicazione v2 per l'aggiornamento. Scaricare l' [applicazione di esempio **WordCount** ](http://aka.ms/servicefabricsamples) (disponibile negli esempi di introduzione). Creare e creare un pacchetto dell'applicazione in Visual Studio (pulsante destro del mouse su **WordCount** in Esplora soluzioni e selezionare **pacchetto**). Copiare il pacchetto v1 in `C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug` a `C:\Temp\WordCount`. Copia `C:\Temp\WordCount` a `C:\Temp\WordCountV2`, la creazione del pacchetto di applicazione v2 per l'aggiornamento. Apri `C:\Temp\WordCountV2\ApplicationManifest.xml` in un editor di testo. Nell'elemento **ApplicationManifest** , modificare l'attributo **ApplicationTypeVersion** da "1.0.0" in "2.0.0" per aggiornare il numero di versione di app. Salvare il file ApplicationManifest.xml modificato.

## <a name="task-deploy-a-service-fabric-application"></a>Attività: Distribuire un'applicazione di servizio tessuti

Dopo aver creato e pacchetto dell'applicazione (o scaricare il pacchetto dell'applicazione), è possibile distribuire l'applicazione in un cluster di tessuti servizio locale. Distribuzione implica durante il caricamento del pacchetto dell'applicazione, registrare il tipo di applicazione e la creazione dell'istanza dell'applicazione. Utilizzare le istruzioni in questa sezione per distribuire una nuova applicazione a un cluster.

### <a name="step-1-upload-the-application-package"></a>Passaggio 1: Caricare il pacchetto dell'applicazione
Caricamento del pacchetto dell'applicazione in archivio immagini inserisce in un percorso accessibile ai componenti dell'infrastruttura di servizio interni.  Pacchetto dell'applicazione contiene la configurazione necessaria manifesto dell'applicazione servizio manifesti e codice e i pacchetti di dati per creare l'applicazione e istanze del servizio.  Il comando [**Copia ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125905.aspx) consente di caricare il pacchetto. Per esempio:

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCount\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

### <a name="step-2-register-the-application-type"></a>Passaggio 2: Registrare il tipo di applicazione
La registrazione del pacchetto di applicazione rende il tipo di applicazione e la versione dichiarato nel manifesto dell'applicazione disponibile per l'uso. Il sistema legge il pacchetto caricato nel passaggio 1, verificare il pacchetto (equivalente all'esecuzione di [**Test ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125950.aspx) in locale), elaborare il contenuto del pacchetto e copiare il pacchetto trasformato in un punto di sistema interno.  Eseguire il cmdlet [**Registro ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx) :

```powershell
Register-ServiceFabricApplicationType WordCount
```
Per visualizzare tutti i tipi di applicazione registrati del cluster, eseguire il cmdlet [Get-ServiceFabricApplicationType](https://msdn.microsoft.com/library/azure/mt125871.aspx) :

```powershell
Get-ServiceFabricApplicationType
```

### <a name="step-3-create-the-application-instance"></a>Passaggio 3: Creare l'istanza dell'applicazione
Tramite una versione di tipo di applicazione che è stata registrata tramite il comando [**Nuovo ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125913.aspx) , è possibile avviare un'applicazione. Il nome di ogni applicazione dichiarato in fase di distribuzione e devono iniziare con la **tessuti:** schema ed essere univoco per ogni istanza dell'applicazione. Il nome del tipo di applicazione e la versione di tipo di applicazione vengono dichiarati nel file **ApplicationManifest.xml** del pacchetto dell'applicazione. Se uno o più servizi predefinito definiti nel manifesto dell'applicazione del tipo di applicazione di destinazione, quelli vengono creati al momento.

```powershell
New-ServiceFabricApplication fabric:/WordCount WordCount 1.0.0
```

Il comando [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx) Elenca tutte le istanze di applicazione create, nonché il relativo stato complessivo. Il comando [**Get-ServiceFabricService**](https://msdn.microsoft.com/library/azure/mt125889.aspx) Elenca tutte le istanze di servizio che sono state create all'interno di un'istanza di applicazione specificato. Sono elencati i servizi predefiniti (se presente).

```powershell
Get-ServiceFabricApplication

Get-ServiceFabricApplication | Get-ServiceFabricService
```

## <a name="task-upgrade-a-service-fabric-application"></a>Attività: Aggiornamento di un'applicazione di servizio tessuti
È possibile aggiornare un'applicazione di servizio tessuti distribuita in precedenza con un pacchetto dell'applicazione aggiornato. Questa operazione consente di aggiornare l'applicazione WordCount distribuiti in "attività: distribuire un'applicazione di servizio tessuti." Per ulteriori informazioni, vedere attraverso [l'aggiornamento dell'applicazione di servizio tessuti](service-fabric-application-upgrade.md) .

Per semplicità per questo esempio, solo il numero di versione dell'applicazione è stato aggiornato nel pacchetto dell'applicazione WordCountV2 creato nei prerequisiti. Uno scenario con più realistico prevede l'aggiornamento dei file di dati, configurazione o codice servizio ricostruzione e imballaggio dell'applicazione con numeri di versione aggiornata.  

### <a name="step-1-upload-the-updated-application-package"></a>Passaggio 1: Caricare il pacchetto di applicazione aggiornato
L'applicazione di v1 WordCount è pronta per l'aggiornamento. Se si apre una finestra di PowerShell come amministratore e digitare [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx), si noterà che versione 1.0.0 del tipo di applicazione WordCount viene distribuito.  

A questo punto copiare il pacchetto di applicazione aggiornato archivio immagini tessuti di servizio (nel punto in cui i pacchetti di applicazione vengono memorizzati dal servizio tessuti). Il parametro **ApplicationPackagePathInImageStore** informa l'infrastruttura di servizio in cui è possibile individuare il pacchetto dell'applicazione. Il comando seguente consente di copiare il pacchetto dell'applicazione **WordCountV2** nell'archivio di immagine:  

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCountV2\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCountV2

```
### <a name="step-2-register-the-updated-application-type"></a>Passaggio 2: Registrare il tipo di applicazione aggiornato
Il passaggio successivo consiste nel registrare la nuova versione dell'applicazione di servizio tessuti, che può essere eseguita utilizzando il cmdlet [**Registro ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx) :

```powershell
Register-ServiceFabricApplicationType WordCountV2
```

### <a name="step-3-start-the-upgrade"></a>Passaggio 3: Avviare l'aggiornamento
Vari i parametri di aggiornamento, timeout e criteri di integrità possono essere applicate agli aggiornamenti delle applicazioni. Leggere i documenti di [parametri di aggiornamento dell'applicazione](service-fabric-application-upgrade-parameters.md) e [processo di aggiornamento](service-fabric-application-upgrade.md) per ottenere ulteriori informazioni. Tutti i servizi e le istanze dovrebbero essere _Integro_ dopo l'aggiornamento.  Impostare **HealthCheckStableDuration** su 60 secondi (in modo che i servizi siano corretti per 20 secondi prima che l'aggiornamento proceda al successivo aggiornamento dominio).  Anche impostare **UpgradeDomainTimeout** 1200 secondi e **UpgradeTimeout** su 3000 secondi. Infine, impostare **UpgradeFailureAction** **ripristino dello stato precedente**, il quale richiede che tessuti servizio consente di ripristinare l'applicazione per la versione precedente se vengono rilevati errori durante l'aggiornamento.

È ora possibile avviare l'aggiornamento dell'applicazione utilizzando il cmdlet [**Start ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125975.aspx) :

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/WordCount -ApplicationTypeVersion 2.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000  -FailureAction Rollback -Monitored
```

Si noti che il nome dell'applicazione è lo stesso nome applicazione versione 1.0.0 distribuite in precedenza (tessuti: / WordCount). Servizio tessuti utilizza questo nome per identificare Guida aggiornamento dell'applicazione. Se si imposta il timeout di troppo breve, possono verificarsi un messaggio di errore di timeout che indica il problema. Consultare la [risoluzione dei problemi di aggiornamento delle applicazioni](service-fabric-application-upgrade-troubleshooting.md)o aumentare il timeout.

### <a name="step-4-check-upgrade-progress"></a>Passaggio 4: Controllo lo stato dell'aggiornamento
È possibile monitorare lo stato dell'aggiornamento dell'applicazione tramite [Esplora file tessuti servizio](service-fabric-visualizing-your-cluster.md)o utilizzando il cmdlet [**Get-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125988.aspx) :

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/WordCount
```

In pochi minuti, il cmdlet [Get-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/azure/mt125988.aspx) mostra che tutti i domini di aggiornamento sono stati aggiornati (completata).

## <a name="task-test-a-service-fabric-application"></a>Attività: Testare un'applicazione di servizio tessuti

Per scrivere i servizi di alta qualità, gli sviluppatori devono essere in grado di indurre gli errori di infrastruttura origini non affidabili per verificare la stabilità dei servizi. Servizio tessuti consente agli sviluppatori di indurre azioni guasto e servizi in presenza di errori di prova usando gli scenari di test chaos e failover.  Per ulteriori informazioni, leggere [Panoramica testabilità](service-fabric-testability-overview.md) .

### <a name="step-1-run-the-chaos-test-scenario"></a>Passaggio 1: Eseguire uno scenario di test chaos
Uno scenario di test chaos genera errori attraverso l'intero cluster di servizio tessuti. Lo scenario comprime errori rilevati in genere in mesi o anni da alcune ore. La combinazione di errori interfoliazione con una frequenza degli errori elevato trova casi angolo che non si in caso contrario verifica. Nell'esempio seguente viene eseguito lo scenario di test chaos per 60 minuti.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```

### <a name="step-2-run-the-failover-test-scenario"></a>Passaggio 2: Eseguire uno scenario di test failover
Scenario destinazioni dei test il failover una partizione di servizio specifico anziché l'intero cluster e altri servizi lascia invariati. Lo scenario scorre una sequenza di simulata errori di convalida servizi durante l'esecuzione di logica aziendale. Un errore di convalida dei servizi indica un problema che deve essere ulteriormente indagini. Provare il provoca guasto solo uno alla volta, anziché lo scenario di test chaos, che può provocare errori più. Nell'esempio seguente viene eseguito il provare per 60 minuti rispetto all'infrastruttura: / WordCount/WordCountService servizio.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/WordCount/WordCountService"

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindUniformInt64 -PartitionKey 1
```

## <a name="task-remove-a-service-fabric-application"></a>Attività: Rimuovere un'applicazione di servizio tessuti
È possibile eliminare un'istanza di un'applicazione distribuita, rimuovere il tipo di applicazione provisioning dal cluster e rimuovere il ImageStore pacchetto dell'applicazione.

### <a name="step-1-remove-an-application-instance"></a>Passaggio 1: Rimuovere un'istanza dell'applicazione
Quando un'istanza dell'applicazione non è più necessaria, è possibile rimuovere definitivamente in utilizzando il cmdlet [**ServiceFabricApplication Rimuovi**](https://msdn.microsoft.com/library/azure/mt125914.aspx) . Verranno rimosse automaticamente anche tutti i servizi che appartengono all'applicazione, in modo permanente la rimozione di tutto lo stato del servizio. Non è possibile annullare l'operazione e lo stato dell'applicazione non può essere recuperato.

```powershell
Remove-ServiceFabricApplication fabric:/WordCount
```

### <a name="step-2-unregister-the-application-type"></a>Passaggio 2: Annullare la registrazione del tipo di applicazione
Quando non è più necessario una versione specifica di un tipo di applicazione, annullarne utilizzando il cmdlet [**ServiceFabricApplicationType annullamento della registrazione**](https://msdn.microsoft.com/library/azure/mt125885.aspx) . Annullamento della registrazione tipi inutilizzati libera spazio di archiviazione utilizzato dal pacchetto dell'applicazione nell'archivio di immagine. Un tipo di applicazione può essere annullato, purché Nessuna applicazione creata per tale oggetto o in sospeso aggiornamenti applicazione farvi riferimento.

```powershell
Unregister-ServiceFabricApplicationType WordCount 1.0.0
```

### <a name="step-3-remove-the-application-package"></a>Passaggio 3: Rimuovere il pacchetto dell'applicazione
Dopo avere inserito il tipo di applicazione non registrato, il pacchetto dell'applicazione può essere rimossa dalla archivio immagini utilizzando il cmdlet [**ServiceFabricApplicationPackage Rimuovi**](https://msdn.microsoft.com/library/azure/mt163532.aspx) .

```powershell
Remove-ServiceFabricApplicationPackage -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

## <a name="next-steps"></a>Passaggi successivi
[Ciclo di vita applicazione servizio tessuti](service-fabric-application-lifecycle.md)

[Distribuire un'applicazione](service-fabric-deploy-remove-applications.md)

[Aggiornamento dell'applicazione](service-fabric-application-upgrade.md)

[Cmdlet di servizio tessuti Azure](https://msdn.microsoft.com/library/azure/mt125965.aspx)

[Cmdlet di testabilità servizio tessuti Azure](https://msdn.microsoft.com/library/azure/mt125844.aspx)
