<properties
   pageTitle="Aggiornamento del servizio tessuti App tramite PowerShell | Microsoft Azure"
   description="In questo articolo vengono illustrati l'esperienza di distribuzione di un'applicazione di servizio tessuti, la modifica del codice e distribuzione di un aggiornamento tramite PowerShell."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>


# <a name="service-fabric-application-upgrade-using-powershell"></a>Aggiornamento dell'applicazione di servizio tessuti tramite PowerShell

> [AZURE.SELECTOR]
- [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
- [Visual Studio](service-fabric-application-upgrade-tutorial.md)

<br/>

Più utilizzate di frequente e consiglia di eseguire l'aggiornamento è l'aggiornamento in sequenza monitorata.  Azure tessuti servizio esegue il monitoraggio dell'integrità dell'applicazione in corso l'aggiornamento in base a un set di criteri di integrità. Dopo aver aggiornato un dominio di aggiornamento (UD), servizio tessuti restituisce lo stato dell'applicazione e procede al successivo aggiornamento dominio o non riesce l'aggiornamento in base ai criteri di integrità.

Un aggiornamento di applicazione può essere eseguito tramite gestito o nativo API, PowerShell o resto. Per istruzioni su come eseguire l'aggiornamento utilizzando Visual Studio, vedere [aggiornamento dell'applicazione tramite Visual Studio](service-fabric-application-upgrade-tutorial.md).

Con tessuti servizio controllare gli aggiornamenti in sequenza, l'amministratore dell'applicazione possibile configurare il criterio di valutazione di integrità servizio tessuti utilizzata per determinare se l'applicazione è integro. Inoltre, l'amministratore può configurare l'azione da eseguire quando la valutazione dell'integrità non riesce (ad esempio, eseguire un ripristino automatico.) In questa sezione vengono esaminati un aggiornamento monitorato per uno degli esempi SDK che utilizza PowerShell.

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>Passaggio 1: Compilare e distribuire il campione di oggetti visivi


Creare e pubblicare l'applicazione del mouse sul progetto applicazione, **VisualObjectsApplication** e selezionare il comando **pubblica** .  Per ulteriori informazioni, vedere [applicazione di servizio tessuti esercitazione di aggiornamento](service-fabric-application-upgrade-tutorial.md).  In alternativa, è possibile utilizzare PowerShell per distribuire l'applicazione.

> [AZURE.NOTE] Prima di uno dei comandi tessuti servizio possono essere usato in PowerShell, è innanzitutto necessario connettersi al cluster tramite il `Connect-ServiceFabricCluster` cmdlet. Analogamente, si presuppone che il Cluster è già stato impostato sul computer locale. Vedere l'articolo [sull'impostazione dell'ambiente di sviluppo servizio tessuti](service-fabric-get-started.md).

Dopo aver creato il progetto in Visual Studio, è possibile utilizzare il comando PowerShell **ServiceFabricApplicationPackage copia** in cui per copiare il ImageStore pacchetto dell'applicazione. Il passaggio successivo consiste nel registrare l'applicazione al runtime del servizio tessuti utilizzando il cmdlet **Registro ServiceFabricApplicationPackage** . Il passaggio finale è per avviare un'istanza dell'applicazione utilizzando il cmdlet **New-ServiceFabricApplication** .  Questi tre passaggi sono analoghi all'utilizzo di voce di menu **Distribuisci** in Visual Studio.

A questo punto, è possibile utilizzare [Servizi tessuti soluzioni per visualizzare il cluster e l'applicazione](service-fabric-visualizing-your-cluster.md). L'applicazione dispone di un servizio web che per spostarsi in Internet Explorer digitando [http://localhost:8081/visualobjects](http://localhost:8081/visualobjects) nella barra degli indirizzi.  Verranno visualizzati alcuni oggetti visual mobile spostamento all'interno della schermata.  Inoltre, è possibile utilizzare **Get-ServiceFabricApplication** per controllare lo stato dell'applicazione.

## <a name="step-2-update-the-visual-objects-sample"></a>Passaggio 2: Aggiornare il campione di oggetti visivi

Si noti che con la versione che è stata distribuita nel passaggio 1, gli oggetti visivi non ruotare. Di seguito, eseguire l'aggiornamento dell'applicazione a uno in cui gli oggetti visivi anche ruotare.

Selezionare il progetto VisualObjects.ActorService all'interno della soluzione VisualObjects e aprire il file StatefulVisualObjectActor.cs. All'interno del file, passare al metodo `MoveObject`, commento `this.State.Move()`e rimuovere il commento `this.State.Move(true)`. Questa modifica ruota gli oggetti dopo il servizio di aggiornamento.

È anche necessario aggiornare il file *ServiceManifest.xml* (in PackageRoot) del progetto **VisualObjects.ActorService**. Aggiornamento a 2.0 e le righe corrispondenti nel file *ServiceManifest.xml* *CodePackage* e la versione del servizio.
È possibile utilizzare l'opzione di Visual Studio *Modifica file manifesto* dopo rapida sulla soluzione per apportare le modifiche di file manifesto.


Una volta apportate le modifiche, il manifesto avrà un aspetto simile al seguente (parti evidenziate consente di visualizzare le modifiche):

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

A questo punto il file *ApplicationManifest.xml* (disponibile in project **VisualObjects** in soluzione **VisualObjects** ) viene aggiornato alla versione 2.0 del progetto **VisualObjects.ActorService** . Inoltre, la versione dell'applicazione viene aggiornata a 2.0.0.0 da 1.0.0.0. *ApplicationManifest.xml* dovrebbe essere simile il frammento di codice seguente:

```xml
<ApplicationManifestxmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```


A questo punto, compilare il progetto selezionando solo nel progetto **ActorService** e quindi facendo clic su e selezionare l'opzione **creare** in Visual Studio. Se si seleziona **Rigenera tutto**, è necessario aggiornare le versioni per tutti i progetti, poiché il codice potrebbe aver modificato. Successivamente, creare un pacchetto di seguito l'applicazione aggiornata mouse su ***VisualObjectsApplication***, selezionando il Menu di tessuti del servizio e scegliendo **pacchetto**. Questa azione consente di creare un pacchetto dell'applicazione che può essere distribuito.  L'applicazione aggiornata è pronto per la distribuzione.


## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>Passaggio 3: Decidere in Criteri di integrità ed eseguire l'aggiornamento di parametri

Acquisire familiarità con i [parametri di aggiornamento dell'applicazione](service-fabric-application-upgrade-parameters.md) e il [processo di aggiornamento](service-fabric-application-upgrade.md) per ottenere una buona conoscenza dei vari parametri di aggiornamento, timeout e criterio di integrità applicato. Per questa procedura dettagliata, il criterio di valutazione di integrità servizio configurare le impostazioni predefinite (e consigliato) i valori, che indica che tutti i servizi e le istanze devono essere _corretti_ dopo l'aggiornamento.  

Tuttavia, si aumenta *HealthCheckStableDuration* su 60 secondi (in modo che i servizi siano corretti per 20 secondi prima che l'aggiornamento proceda al successivo aggiornamento dominio).  Impostare anche *UpgradeDomainTimeout* essere 1200 secondi e *UpgradeTimeout* essere 3000 secondi.

Infine, anche impostare *UpgradeFailureAction* per eseguire il ripristino. Questa opzione richiede tessuti servizio ripristinare l'applicazione per la versione precedente se vengono rilevati problemi durante l'aggiornamento. In questo modo, quando si avvia l'aggiornamento (nel passaggio 4), vengono specificati i parametri seguenti:

FailureAction = ripristino dello stato precedente

HealthCheckStableDurationSec = 60

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000


## <a name="step-4-prepare-application-for-upgrade"></a>Passaggio 4: Preparare applicazione per l'aggiornamento

A questo punto l'applicazione è compilato e pronto per essere aggiornati. Se si apre una finestra di PowerShell come amministratore e tipo **Get-ServiceFabricApplication**, è necessario consentono di si sa che è il tipo di applicazione 1.0.0.0 di **VisualObjects** che è stato distribuito.  

Pacchetto dell'applicazione viene memorizzato con il percorso seguente nel punto in cui si non compressi Service tessuti SDK - *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*. È necessario trovare una cartella "Pacchetto" nella directory, in cui è archiviato il pacchetto dell'applicazione. Selezionare data e ora per verificare che sia la versione più recente (potrebbe essere necessario modificare i percorsi in modo appropriato anche).

A questo punto si copiare il pacchetto di applicazione aggiornato il ImageStore tessuti di servizio (nel punto in cui i pacchetti di applicazione vengono memorizzati dal servizio tessuti). Il parametro *ApplicationPackagePathInImageStore* informa l'infrastruttura di servizio in cui è possibile individuare il pacchetto dell'applicazione. È stata elaborata l'applicazione aggiornata "VisualObjects\_V2" con il comando seguente (potrebbe essere necessario modificare nuovamente in modo appropriato i percorsi).

```powershell
Copy-ServiceFabricApplicationPackage  -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package
-ImageStoreConnectionString fabric:ImageStore   -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

Il passaggio successivo consiste nel registrare l'applicazione di servizio tessuti, che può essere eseguita tramite il comando seguente:

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Se il comando precedente non viene eseguito correttamente, è probabile che sia necessario ricostruzione di tutti i servizi. Come descritto nel passaggio 2, potrebbe essere necessario aggiornare anche la versione di servizio.

## <a name="step-5-start-the-application-upgrade"></a>Passaggio 5: Avviare l'aggiornamento dell'applicazione

A questo punto, è stiamo impostare per avviare l'aggiornamento dell'applicazione tramite il comando seguente:

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


Il nome dell'applicazione è quello descritto nel file *ApplicationManifest.xml* . Servizio tessuti utilizza questo nome per identificare Guida aggiornamento dell'applicazione. Se si imposta il timeout sia troppo breve, è possibile riscontrare un messaggio di errore che indica il problema. Fare riferimento alla sezione sulla risoluzione dei problemi o aumentare il timeout.

A questo punto, durante l'applicazione aggiornamento, è possibile monitorarlo servizio tessuti Explorer o tramite PowerShell seguente comando: **tessuti Get-ServiceFabricApplicationUpgrade: / VisualObjects**.

In pochi minuti, lo stato ottenuto utilizzando il comando PowerShell precedente, devono essere specificati che tutti i domini di aggiornamento sono stati aggiornati (completata). E si trova che gli oggetti visivi nella finestra del browser hanno avviato rotazione!

È possibile provare l'aggiornamento dalla versione 2 alla versione 3 o dalla versione 2 alla versione 1 come esercizio. Spostamento dalla versione 2 alla versione 1 viene considerato anche un aggiornamento. Riprodurre con timeout e criteri di integrità effettuare manualmente familiarità con esse. Quando si distribuisce in un cluster di Azure, è necessario impostare in modo appropriato i parametri. È consigliabile impostare il timeout ripagati.


## <a name="next-steps"></a>Passaggi successivi

[L'aggiornamento dell'applicazione utilizzando Visual Studio](service-fabric-application-upgrade-tutorial.md) fornisce un aggiornamento dell'applicazione utilizzando Visual Studio.

È possibile controllare come l'applicazione viene aggiornata utilizzando [Parametri aggiornamento](service-fabric-application-upgrade-parameters.md).

Effettuare l'aggiornamento delle applicazioni compatibili per imparare a utilizzare [serializzazione dei dati](service-fabric-application-upgrade-data-serialization.md).

Informazioni su come usare la funzionalità avanzate durante l'aggiornamento dell'applicazione facendo riferimento a [argomenti avanzati](service-fabric-application-upgrade-advanced.md).

Risolvere i problemi comuni in aggiornamenti dell'applicazione facendo riferimento alla procedura nella [risoluzione dei problemi di aggiornamento dell'applicazione](service-fabric-application-upgrade-troubleshooting.md).
