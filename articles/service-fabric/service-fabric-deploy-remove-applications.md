<properties
   pageTitle="Distribuzione di applicazioni di servizio tessuti | Microsoft Azure"
   description="Come distribuire e rimuovere le applicazioni di servizio tessuti"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>

# <a name="deploy-and-remove-applications-using-powershell"></a>Distribuire e rimuovere le applicazioni che utilizzano PowerShell

> [AZURE.SELECTOR]
- [PowerShell](service-fabric-deploy-remove-applications.md)
- [Visual Studio](service-fabric-publish-app-remote-cluster.md)

<br/>

Una volta un [tipo di applicazione compresso][10], è pronto per la distribuzione in un cluster di Azure servizio tessuti. Distribuzione prevede tre passaggi seguenti:

1. Caricare il pacchetto dell'applicazione
2. Registrare il tipo di applicazione
3. Creare l'istanza dell'applicazione

>[AZURE.NOTE] Se si utilizza Visual Studio per la distribuzione e il debug di applicazioni il cluster sviluppo locale, tutti i passaggi seguenti vengono gestiti automaticamente tramite uno script di PowerShell disponibile nella cartella script del progetto di applicazione. In questo articolo vengono fornite base sulle cosa stanno facendo gli script in modo che sia possibile eseguire le stesse operazioni all'esterno di Visual Studio.

## <a name="upload-the-application-package"></a>Caricare il pacchetto dell'applicazione

Durante il caricamento del pacchetto dell'applicazione la inserisce in un percorso accessibile da componenti interni di servizio tessuti. È possibile usare PowerShell per eseguire il caricamento. Prima di eseguire i comandi di PowerShell in questo articolo, avviare sempre mediante [ServiceFabricCluster Connetti](https://msdn.microsoft.com/library/mt125938.aspx) a connettersi al cluster servizio tessuti.

Si supponga che una cartella denominata *MyApplicationType* che contiene il manifesto necessarie, manifesti servizio e pacchetti/config/i dati dei codici. Il comando [Copia ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/mt125905.aspx) consente di caricare il pacchetto cluster archivio immagini. Il cmdlet **Get-ImageStoreConnectionStringFromClusterManifest** che fa parte del modulo servizio tessuti SDK PowerShell, viene utilizzato per ottenere la stringa di connessione di archivio di immagine.  Per importare il modulo SDK, eseguire:

```
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

È possibile copiare un pacchetto di applicazioni da *Studio C:\users\ryanwi\Documents\Visual 2015\Projects\MyApplication\myapplication\pkg\debug* a *c:\temp\MyApplicationType* (Rinomina directory "debug" a "MyApplicationType"). Nell'esempio riportato di seguito viene caricato il pacchetto:

~~~
PS C:\temp> dir

    Directory: c:\temp


Mode                LastWriteTime         Length Name                                                                                   
----                -------------         ------ ----                                                                                   
d-----        8/12/2016  10:23 AM                MyApplicationType                                                                          

PS C:\temp> tree /f .\Stateless1Pkg

C:\TEMP\MyApplicationType
│   ApplicationManifest.xml
|
└───Stateless1Pkg
  	|   ServiceManifest.xml
    │
    └───Code
    │   │  Microsoft.ServiceFabric.Data.dll
    │   │  Microsoft.ServiceFabric.Data.Interfaces.dll
    │   │  Microsoft.ServiceFabric.Internal.dll
    │   │  Microsoft.ServiceFabric.Internal.Strings.dll
    │   │  Microsoft.ServiceFabric.Services.dll
    │   │  ServiceFabricServiceModel.dll
    │   │  MyService.exe
    │   │  MyService.exe.config
    │   │  MyService.pdb
    │   │  System.Fabric.dll
    │   │  System.Fabric.Strings.dll
    │   │
    │   └───en-us
  	|         Microsoft.ServiceFabric.Internal.Strings.resources.dll
  	|         System.Fabric.Strings.resources.dll
  	|
    ├───Config
    │     Settings.xml
    │
    └───Data
          init.dat

PS C:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath MyApplicationType -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))
Copy application package succeeded

PS D:\temp>
~~~

## <a name="register-the-application-package"></a>Registrare il pacchetto dell'applicazione

La registrazione del pacchetto di applicazione rende il tipo di applicazione e la versione dichiarato nel manifesto dell'applicazione disponibile per l'uso. Il sistema legge il pacchetto caricato nel passaggio precedente, verificare il pacchetto (equivalente all'esecuzione di [Test ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/mt125950.aspx) in locale), elaborare il contenuto del pacchetto e copiare il pacchetto trasformato in un punto di sistema interno.

~~~
PS D:\temp> Register-ServiceFabricApplicationType MyApplicationType
Register application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp>
~~~

Il comando [Registro ServiceFabricApplicationType](https://msdn.microsoft.com/library/mt125958.aspx) restituisce solo dopo che il sistema è copiato correttamente il pacchetto dell'applicazione. Il tempo necessario dipende dal contenuto del pacchetto dell'applicazione. Se necessario, è possibile utilizzare il parametro **- TimeoutSec** per fornire un timeout più lungo. (Il timeout predefinito è 60 secondi)

Il comando [Get-ServiceFabricApplicationType](https://msdn.microsoft.com/library/mt125871.aspx) Elenca tutte le versioni di tipo di applicazione correttamente registrati.

## <a name="create-the-application"></a>Creare l'applicazione

Creare un'applicazione tramite una versione di tipo di applicazione che è stata registrata tramite il comando [Nuovo ServiceFabricApplication](https://msdn.microsoft.com/library/mt125913.aspx) . Il nome di ogni applicazione deve iniziare con la *tessuti:* schema ed essere univoco per ogni istanza dell'applicazione. In questa fase vengono creati uno o più servizi predefinito in manifesto dell'applicazione del tipo di applicazione di destinazione.

~~~
PS D:\temp> New-ServiceFabricApplication fabric:/MyApp MyApplicationType AppManifestVersion1

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationStatus      : Ready
HealthState            : OK
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/MyService
ServiceKind            : Stateless
ServiceTypeName        : MyServiceType
IsServiceGroup         : False
ServiceManifestVersion : SvcManifestVersion1
ServiceStatus          : Active
HealthState            : Ok

PS D:\temp>
~~~

Il comando [Get-ServiceFabricApplication](https://msdn.microsoft.com/library/mt163515.aspx) Elenca tutte le istanze di applicazione create, nonché il relativo stato complessivo.

Il comando [Get-ServiceFabricService](https://msdn.microsoft.com/library/mt125889.aspx) Elenca tutte le istanze di servizio che sono state create all'interno di un'istanza di applicazione specificato. Servizi predefiniti (se presente) sono elencati qui.

Per qualsiasi versione specificata di un tipo di applicazione registrata, è possibile creare più istanze di applicazione. Ogni istanza dell'applicazione viene eseguita separatamente, con il proprio lavoro directory e processo.

## <a name="remove-an-application"></a>Rimuovere un'applicazione

Quando un'istanza dell'applicazione non è più necessaria, è possibile rimuovere definitivamente tramite il comando [Rimuovi ServiceFabricApplication](https://msdn.microsoft.com/library/mt125914.aspx) . Questo comando verranno rimosse automaticamente tutti i servizi che appartengono all'applicazione, in modo permanente la rimozione di tutto lo stato del servizio. Non è possibile annullare l'operazione e lo stato dell'applicazione non può essere recuperato.

~~~
PS D:\temp> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS D:\temp> Get-ServiceFabricApplication
PS D:\temp>
~~~

Quando una versione specifica di un tipo di applicazione non è più necessaria, si deve essere annullata tramite il comando [Annulla registrazione ServiceFabricApplicationType](https://msdn.microsoft.com/library/mt125885.aspx) . Annullamento della registrazione tipi inutilizzati rilascia spazio di archiviazione utilizzato per il contenuto del pacchetto dell'applicazione di quel tipo di archivio immagine. Un tipo di applicazione può essere annullato, purché Nessuna applicazione vengono creata sulla base di essa e non in sospeso applicazione aggiornamenti sono farvi riferimento.

~~~
PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp> Unregister-ServiceFabricApplicationType MyApplicationType AppManifestVersion1
Unregister application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

PS D:\temp>
~~~

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copia ServiceFabricApplicationPackage chiesta un ImageStoreConnectionString

Ambiente servizio tessuti SDK dovrebbe già disporre le impostazioni predefinite corrette configurare. Ma se necessario, ImageStoreConnectionString per tutti i comandi deve corrispondere al valore che utilizza cluster servizio tessuti. È possibile trovare questo nel manifesto cluster recuperato tramite il comando [Get-ServiceFabricClusterManifest](https://msdn.microsoft.com/library/mt126024.aspx) :

~~~
PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType

cmdlet Copy-ServiceFabricApplicationPackage at command pipeline position 1
Supply values for the following parameters:
ImageStoreConnectionString:

PS D:\temp> Get-ServiceFabricClusterManifest
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]

PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType -ImageStoreConnectionString file:D:\ServiceFabric\Data\ImageStore
Copy application package succeeded

PS D:\temp>
~~~

## <a name="next-steps"></a>Passaggi successivi

[Aggiornamento dell'applicazione di servizio tessuti](service-fabric-application-upgrade.md)

[Introduzione di integrità servizio tessuti](service-fabric-health-introduction.md)

[Individuare e risolvere i problemi di un servizio di assistenza tessuti](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modello di un'applicazione di servizio tessuti](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md
