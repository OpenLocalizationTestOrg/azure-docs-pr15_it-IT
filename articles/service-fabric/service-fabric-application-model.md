<properties
   pageTitle="Modello di applicazione di servizio tessuti | Microsoft Azure"
   description="Come modello e descrivono le applicazioni e servizi di infrastruttura di servizio."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor="mani-ramaswamy"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"   
   ms.author="seanmck"/>

# <a name="model-an-application-in-service-fabric"></a>Modello di un'applicazione di servizio tessuti

In questo articolo viene fornita una panoramica del modello di applicazione di Azure servizio tessuti. Viene inoltre descritto come definire un'applicazione e servizio tramite file manifesto e ottenere l'applicazione compresso e pronto per la distribuzione.

## <a name="understand-the-application-model"></a>Informazioni sul modello di applicazione

Un'applicazione è un insieme di servizi costitutivi che eseguono una determinata funzione o funzioni. Un servizio esegue una funzione di completamento e autonomo (è possibile avviare ed eseguire indipendentemente da altri servizi) ed è composta codice, configurazione e i dati. Per ogni servizio codice è costituito da binari eseguibili, configurazione è costituito da impostazioni del servizio che possono essere caricate in fase di esecuzione e dati è costituito da dati statici non autorizzati che verrà utilizzata dal servizio. Ogni componente in questo modello di applicazione gerarchico può essere versione e aggiornate in modo indipendente.

![Il modello di applicazione di servizio tessuti][appmodel-diagram]


Un tipo di applicazione è una classificazione di un'applicazione ed è costituito da un gruppo di tipi di servizio. Un tipo di servizio è la categorizzazione di un servizio. Categorizzazione può avere diverse impostazioni e configurazioni, ma le funzionalità di base rimangono inalterato. Le istanze di un servizio sono le varianti di configurazione di servizi diversa dello stesso tipo di servizio.  

Le classi (o "tipo") sono descritti tramite file XML (i manifesti di applicazioni e servizi) che sono i modelli per la quale è possono avviare applicazioni dall'archivio di immagini del cluster di applicazioni e servizi. Definizione dello schema per il file ServiceManifest.xml e ApplicationManifest.xml viene installata con il servizio tessuti SDK e strumenti per *C:\Programmi\Microsoft c:\Programmi\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

Il codice per le istanze di applicazione diverso verrà eseguito come processi distinti anche quando ospitato in stesso nodo servizio tessuti. Inoltre, è possibile gestire (ad esempio il ciclo di vita di ogni istanza dell'applicazione aggiornare) in modo indipendente. Il diagramma seguente illustra come tipi di applicazione sono costituiti da tipi di servizi, che a sua volta sono costituiti da codice, configurazione e pacchetti. Per semplificare il diagramma, solo il codice / / dati configurazione pacchetti per `ServiceType4` si riferiscono, nonostante ogni tipo di servizio includerà alcuni o tutti i tipi di imballaggio.

![Tipi di applicazioni di servizio tessuti e tipi di servizio][cluster-imagestore-apptypes]

Due diversi file manifesto vengono utilizzati per descrivere le applicazioni e servizi: manifesti applicazione e il servizio. Questi sono trattati nelle sezioni risultante.

Può essere attivo nel cluster uno o più istanze di un tipo di servizio. Ad esempio istanze informazioni sullo stato del servizio, o repliche, ottenere affidabilità mediante la replica di stato tra repliche presenti in diversi nodi del cluster. La replica essenzialmente fornisce ridondanza per il servizio sia disponibile anche se si verifica un errore di un nodo in un cluster. Un [servizio partizionata](service-fabric-concepts-partitioning.md) ulteriormente divide il relativo stato (e modelli di accesso a tale stato) nei nodi del cluster.

Il diagramma seguente illustra la relazione tra le applicazioni e le istanze del servizio, le partizioni e repliche.

![Partizioni e repliche all'interno di un servizio][cluster-application-instances]


>[AZURE.TIP] È possibile visualizzare il layout delle applicazioni in un cluster utilizzando lo strumento di servizio tessuti Explorer disponibile in http://&lt;yourclusteraddress&gt;: 19080/Explorer. Per ulteriori informazioni, vedere [la visualizzazione del cluster con Esplora risorse tessuti servizio](service-fabric-visualizing-your-cluster.md).

## <a name="describe-a-service"></a>Descrivere un servizio

Manifesto servizio in modo dichiarativo definisce il tipo di servizio e la versione. Specifica metadati del servizio, ad esempio tipo di servizio, le proprietà di integrità, metriche di bilanciamento del carico, file binari del servizio e file di configurazione.  In altre parole, vengono descritti i pacchetti di codice, configurazione e i dati che costituiscono un pacchetto di servizio per uno o più tipi di servizi di supporto. Ecco un esempio semplice di manifesto di servizio:

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="MyCode" Version="CodeVersion1">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="MyConfig" Version="ConfigVersion1" />
  <DataPackage Name="MyData" Version="DataVersion1" />
</ServiceManifest>
~~~

Gli attributi di **versione** sono stringhe non strutturate e non analizzato dal sistema. Questi sono utilizzati per ogni componente per gli aggiornamenti.

**ServiceTypes** dichiara quali tipi di servizio sono supportati da **CodePackages** in questo manifesto. Quando si crea un'istanza di un servizio rispetto a uno di questi tipi di servizio, tutti i pacchetti di codice dichiarati in questo manifesto sono attivati eseguendo i punti di ingresso. I processi risultanti previsto per registrare i tipi di servizio supportati in fase di esecuzione. Si noti che i tipi di servizio sono dichiarati livello manifesto e non il livello di pacchetto codice. Pertanto quando sono presenti più pacchetti di codice, vengono tutti attivati ogni volta che il sistema di ricerca per uno qualsiasi dei tipi di servizio dichiarato.

**SetupEntryPoint** è un punto di ingresso privilegi che viene eseguita con le stesse credenziali servizio tessuti (in genere l'account *LocalSystem* ) prima di qualsiasi altro punto di ingresso. Il file eseguibile specificato dal **punto di ingresso** è in genere l'host del servizio di esecuzione prolungata. La presenza di un punto di ingresso installazione separata consente di evitare la necessità di eseguire l'host del servizio con privilegi elevati per lunghi periodi di tempo. L'eseguibile specificato dal **punto di ingresso** viene eseguito dopo **SetupEntryPoint** chiusura correttamente. Il processo risultante viene controllato e si riavvia, che iniziano con **SetupEntryPoint**, se mai termina o si blocca.

**DataPackage** dichiara una cartella denominata per l'attributo **Name** , che contiene dati statici non autorizzati che verrà utilizzata dal processo in fase di esecuzione.

**ConfigPackage** dichiara una cartella denominata per l'attributo **Name** , che contiene un file *Settings* . Il file contiene sezioni delle impostazioni di eseguire l'associazione definite dall'utente, valore chiave in grado di leggere il processo di nuovo in fase di esecuzione. Durante l'aggiornamento, se solo la **versione** di **ConfigPackage** è stato modificato, quindi il processo in esecuzione non si riavvia. Se, tuttavia, un callback informa il processo di configurazione sono state modificate in modo che possono essere ricaricare in modo dinamico. Di seguito è riportato un file di *Settings* :

~~~
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSecion">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
~~~

> [AZURE.NOTE] Un manifesto servizio può contenere più codice, configurazione e i pacchetti di dati. Ognuno di essi può essere versione in modo indipendente.

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Resources
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application"></a>Descrivere un'applicazione


Manifesto dell'applicazione in modo dichiarativo descrive il tipo di applicazione e la versione. Consente di specificare i metadati di composizione del servizio, ad esempio nomi stabili, partizione combinazione, fattore di conteggio/replica istanza, criteri di sicurezza/isolamento, vincoli di posizionamento, override di configurazione e tipi di servizio costitutivi. Inoltre sono descritti i domini di bilanciamento del carico in cui è inserito l'applicazione.

In questo modo, un manifesto dell'applicazione vengono descritti gli elementi a livello di applicazione e fa riferimento a uno o più manifesti di servizio per la creazione di un tipo di applicazione. Ecco un manifesto dell'applicazione di esempio:

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ApplicationManifest
      ApplicationTypeName="MyApplicationType"
      ApplicationTypeVersion="AppManifestVersion1"
      xmlns="http://schemas.microsoft.com/2011/01/fabric"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example application manifest</Description>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyServiceManifest" ServiceManifestVersion="SvcManifestVersion1"/>
  </ServiceManifestImport>
  <DefaultServices>
     <Service Name="MyService">
         <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
             <SingletonPartition/>
         </StatelessService>
     </Service>
  </DefaultServices>
</ApplicationManifest>
~~~

Ad esempio servizio manifesti, gli attributi di **versione** sono stringhe non strutturate e non vengono analizzati dal sistema. Sono inoltre utilizzati per ogni componente per gli aggiornamenti.

**ServiceManifestImport** contiene riferimenti a manifesti servizio che compongono questo tipo di applicazione. Servizio importati manifesti determinano quali tipi di servizio validi all'interno di questo tipo di applicazione.

**DefaultServices** dichiara istanze del servizio che vengono create automaticamente ogni volta che si crea un'istanza di un'applicazione da questo tipo di applicazione. Servizi predefiniti sono solo una comodità e si comportano come normali servizi ogni riguardo dopo che sono state create. Essi vengono aggiornati insieme a tutti gli altri servizi nell'istanza dell'applicazione e può essere rimossa anche.

> [AZURE.NOTE] Un manifesto può contenere più importazioni manifesto servizio e servizi predefiniti. È possibile versione ogni importazione manifesto servizio in modo indipendente.

Per informazioni su come gestire applicazioni diverso e parametri del servizio per gli ambienti singoli, vedere [gestione dell'applicazione parametri per più ambienti](service-fabric-manage-multiple-environment-app-configuration.md).

<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Application parameters
*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->

## <a name="package-an-application"></a>Pacchetto di un'applicazione

### <a name="package-layout"></a>Layout per il pacchetto

Manifesto dell'applicazione, manifesti del servizio e altri file di pacchetto necessarie devono essere organizzati in un layout specifico per la distribuzione in un cluster di servizio tessuti. Manifesti di esempio in questo articolo dovrà essere organizzati in struttura di directory seguente:

~~~
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
~~~

Le cartelle sono denominate in modo che corrispondano gli attributi di **nome** di ogni elemento corrispondente. Ad esempio, se manifesto servizio contiene due pacchetti di codice con i nomi **MyCodeA** e **MyCodeB**, quindi due cartelle con lo stesso nome contiene file binari necessari per ogni pacchetto di codice.

### <a name="use-setupentrypoint"></a>Utilizzare SetupEntryPoint

Scenari tipici per l'uso di **SetupEntryPoint** sono quando è necessario eseguire un eseguibile prima del servizio o per eseguire un'operazione con privilegi elevati. Per esempio:

- Impostazione e inizializzazione di variabili di ambiente necessarie per il file eseguibile del servizio. Questo non è limitato a solo eseguibili scritti tramite i modelli di programmazione servizio tessuti. Ad esempio, npm.exe deve alcune variabili di ambiente configurati per la distribuzione di un'applicazione di Node.

- Impostazione di controllo dell'accesso installando i certificati di protezione.

### <a name="build-a-package-by-using-visual-studio"></a>Creare un pacchetto utilizzando Visual Studio

Se si utilizza Visual Studio 2015 per creare l'applicazione, è possibile utilizzare il comando pacchetto per creare automaticamente un pacchetto che corrisponde al layout descritto in precedenza.

Per creare un pacchetto, fare clic sul progetto applicazione in Esplora soluzioni e scegliere il comando pacchetto, come illustrato di seguito:

![Imballaggio un'applicazione con Visual Studio][vs-package-command]

Una volta completato confezioni per i prodotti, si noterà la posizione del pacchetto nella finestra di **Output** . Si noti che il passaggio imballaggio si verifica automaticamente quando si distribuisce o il debug dell'applicazione in Visual Studio.

### <a name="test-the-package"></a>Testare il pacchetto

È possibile verificare la struttura del pacchetto localmente tramite PowerShell tramite il comando **ServiceFabricApplicationPackage Test** . Questo comando verificare la presenza di problemi relativi all'analisi manifesto e verificare tutti i riferimenti. Si noti che questo comando verifica solo la correttezza strutturale delle directory e file del pacchetto. Verificherà non i contenuti di pacchetto dati o codice oltre il controllo che tutti i file siano presenti.

~~~
PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
~~~

Questo errore indica che il file *MySetup.bat* riferimento nel servizio manifesto **SetupEntryPoint** disponibili nel pacchetto di codice. Dopo l'aggiunta di file mancante passa la verifica dell'applicazione:

~~~
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat

PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
True
PS D:\temp>
~~~

Dopo l'applicazione viene fornita correttamente e passa verifica, è pronto per la distribuzione.

## <a name="next-steps"></a>Passaggi successivi

[Distribuire e rimozione delle applicazioni][10]

[Gestione dei parametri di applicazione per gli ambienti più][11]

[RunAs: Esecuzione di un'applicazione di servizio tessuti con diverse impostazioni di sicurezza][12]

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png
[vs-package-command]: ./media/service-fabric-application-model/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md
