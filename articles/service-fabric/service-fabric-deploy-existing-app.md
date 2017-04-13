<properties
   pageTitle="Distribuire un eseguibile esistente in Azure servizio tessuti | Microsoft Azure"
   description="Procedura dettagliata su come creare un pacchetto un'applicazione esistente come guest eseguibile, in modo che possa essere distribuito in un cluster di tessuti di servizio"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/22/2016"
   ms.author="msfussell;mikhegn"/>

# <a name="deploy-a-guest-executable-to-service-fabric"></a>Distribuire guest eseguibile tessuti servizio

È possibile eseguire qualsiasi tipo di applicazione, ad esempio Node, Java o applicazioni native in Azure servizio tessuti. Servizio tessuti fa riferimento a questi tipi di applicazioni come guest eseguibili.
Guest eseguibili vengono gestiti dal servizio tessuti come servizi senza informazioni sullo stato. Di conseguenza, vengono inseriti in un cluster, in base a disponibilità e altre metriche i nodi. In questo articolo viene descritto come creare un pacchetto e distribuire guest eseguibile in un cluster di servizio tessuti, utilizzando Visual Studio o della riga di comando.

In questo articolo sono illustrate la procedura per creare un pacchetto guest eseguibile e distribuirlo a infrastruttura di servizio.  

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Vantaggi dell'esecuzione guest eseguibile nel servizio tessuti

Esistono diversi vantaggi che è dotato di esecuzione guest eseguibile in un cluster di tessuti di servizio:

- Disponibilità. Applicazioni che vengono eseguite in tessuti servizio altamente disponibili. Servizio tessuti garantisce che eseguono istanze di un'applicazione.
- Il monitoraggio dell'integrità. Il monitoraggio dell'integrità servizio tessuti rileva se un'applicazione è in esecuzione e vengono fornite informazioni diagnostiche se si verifica un errore.   
- Gestione del ciclo di vita delle applicazioni. Oltre a forniscono aggiornamenti senza tempi di inattività, tessuti servizio fornisce ripristino automatico per la versione precedente nel caso di un evento di stato errate segnalato durante l'aggiornamento.    
- Densità. È possibile eseguire più applicazioni in un cluster, non è più necessario per ogni applicazione di eseguire il proprio hardware.


## <a name="overview-of-application-and-service-manifest-files"></a>Panoramica dell'applicazione e file manifesto del servizio

Come parte della distribuzione guest eseguibile, è utile comprendere la confezione di un servizio tessuti e modello di distribuzione come descritto [modello dell'applicazione](service-fabric-application-model.md). Il modello di imballaggio tessuti servizio si basa su due file XML: manifesti servizi e applicazioni. Definizione dello schema per i file ApplicationManifest.xml e ServiceManifest.xml viene installata con Service tessuti SDK in *C:\Programmi\Microsoft c:\Programmi\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Manifesto dell'applicazione** Manifesto dell'applicazione viene utilizzato per descrivere l'applicazione. Vengono elencati i servizi che compongono it e gli altri parametri che consentono di definire come cui devono essere distribuiti uno o più servizi, ad esempio il numero di istanze.

  In tessuti servizio un'applicazione è un'unità di distribuzione e all'aggiornamento. È possibile aggiornare un'applicazione come una singola unità in cui vengono gestiti i potenziali errori e potenziali ripristini dello stato precedente. Servizio tessuti garantisce che il processo di aggiornamento è completato, o, se l'aggiornamento ha esito negativo, non lasciare l'applicazione sconosciuto/instabile.

* **Servizio manifesto** Manifesto servizio vengono descritti i componenti di un servizio. Include i dati, ad esempio il nome e il tipo di servizio e il relativo codice, configurazione e dati. Manifesto servizio include anche alcuni altri parametri che possono essere utilizzati per configurare il servizio dopo la distribuzione.


## <a name="application-package-file-structure"></a>Struttura di file del pacchetto dell'applicazione
Per distribuire un'applicazione di servizio tessuti, è necessario seguire una struttura di directory predefinite dell'applicazione. Nell'esempio seguente della struttura.

```
|-- ApplicationPackageRoot
  	|-- GuestService1Pkg
        |-- Code
            |-- existingapp.exe
        |-- Config
            |-- Settings.xml
        |-- Data
        |-- ServiceManifest.xml
  	|-- ApplicationManifest.xml
```

La ApplicationPackageRoot contiene il file ApplicationManifest.xml che definisce l'applicazione. Una sottodirectory per ogni servizio incluso nell'applicazione viene utilizzata per contenere tutti gli elementi che è necessario il servizio, ossia il ServiceManifest.xml e in genere tre directory seguenti:

- *Codice*. Questa directory contiene il codice del servizio.
- *Configurazione*. Questa directory contiene un file Settings (e altri file, se necessario) che il servizio può accedere in fase di esecuzione per recuperare le impostazioni di configurazione specifiche.
- *Dati*. Si tratta di una directory aggiuntiva per memorizzare dati locali aggiuntivi che potrebbe essere necessario il servizio. Nota: Dati dovrebbero essere usati per archiviare solo temporanee dati. Servizio tessuti non copia/replica delle modifiche alla directory dati se il servizio deve essere spostato, ad esempio, durante il failover.

Nota: Non è necessario creare la `config` e `data` directory se non sono necessari.

## <a name="packaging-an-existing-executable"></a>Imballaggio un eseguibile esistente

Quando imballaggio un eseguibile guest, è possibile scegliere di utilizzare un modello di progetto di Visual Studio o [creare manualmente il pacchetto dell'applicazione](#manually). Utilizzando Visual Studio, la struttura del pacchetto di applicazioni e file manifesto vengono creati per la creazione guidata nuovo progetto dell'utente.

>[AZURE.NOTE] Il modo più semplice per creare un pacchetto un eseguibile in un servizio di Windows esistente consiste nell'utilizzare Visual Studio.

## <a name="using-visual-studio-to-package-an-existing-executable"></a>Utilizzo di Visual Studio per creare un pacchetto un eseguibile esistente

Visual Studio fornisce un modello di servizio servizio tessuti per informazioni su come distribuire guest eseguibile in un cluster di servizio tessuti.

Eseguire la procedura seguente per completare la pubblicazione:

1. Scegliere File -> Nuovo progetto, creare un'applicazione di servizio tessuti.
2. Scegliere file eseguibile Guest come il modello di servizio.
3. Fare clic su Sfoglia per selezionare la cartella nel file eseguibile e compilare il resto dei parametri per creare il servizio.
    - Per copiare tutto il contenuto della cartella per il progetto di Visual Studio, che risulta utile se il file eseguibile non cambia, è possibile impostare *Comportamento del pacchetto di codice* . Se si prevede che il file eseguibile da modificare e la possibilità di sollevare build di nuovo in modo dinamico, è possibile collegarsi alla cartella. Si noti che è possibile utilizzare cartelle collegate durante la creazione del progetto di applicazione in Visual Studio. Questo fornisce collegamenti alla posizione di origine all'interno del progetto, che consente di aggiornare guest eseguibile di destinazione di origine, in caso gli aggiornamenti diventano parte del pacchetto dell'applicazione in fase di compilazione.
    - *Programma* - scegliere il file eseguibile che deve essere eseguito per avviare il servizio.
    - *Gli argomenti* - specificare gli argomenti da passare al file eseguibile. Può trattarsi di un elenco dei parametri con argomenti.
    - *WorkingFolder* - specifica la cartella di lavoro per il processo che sta per essere avviato. È possibile specificare tre valori:
        - `CodeBase`Specifica che la cartella di lavoro verrà impostata a directory del codice nel pacchetto dell'applicazione (`Code` directory nella struttura di file visualizzata precedente).
        - `CodePackage`Specifica che la cartella di lavoro verrà impostata per la radice del pacchetto dell'applicazione (`GuestService1Pkg` nella struttura di file visualizzata precedente).
        - `Work`Specifica che i file vengono inseriti in una sottodirectory denominata lavoro
4. Specificare un nome per il servizio e fare clic su OK.
5. Se il servizio necessita di un estremo per la comunicazione, è possibile aggiungere a questo punto il protocollo, le porte e tipo di file ServiceManifest.xml. e.g. `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. È ora possibile utilizzare il pacchetto e pubblicare azione contro il cluster locale utilizzando il debug la soluzione in Visual Studio. Quando si è pronti è possibile pubblicare l'applicazione in un cluster remoto o archivia la soluzione al controllo origine.
7. Passare alla fine di questo articolo per informazioni su come visualizzare servizio eseguibile guest in esecuzione in Esplora tessuti servizio.

<a id="manually"></a>
## <a name="manually-packaging-and-deploying-an-existing-executable"></a>Creazione manuale del pacchetto e distribuzione di un eseguibile esistente
Il processo di creazione manuale del pacchetto di un file eseguibile guest si basa sulla procedura seguente:

1. Creare la struttura di directory pacchetto.
2. Aggiungere file di codice e la configurazione dell'applicazione.
3. Modificare il file manifesto servizio.
4. Modificare il file manifesto dell'applicazione.

<!--
>[AZURE.NOTE] We do provide a packaging tool that allows you to create the ApplicationPackage automatically. The tool is currently in preview. You can download it from [here](http://aka.ms/servicefabricpacktool).
-->

### <a name="create-the-package-directory-structure"></a>Creare la struttura di directory pacchetto
È possibile iniziare creando la struttura della directory, come descritto in precedenza.

### <a name="add-the-applications-code-and-configuration-files"></a>Aggiungere file di codice e la configurazione dell'applicazione
Dopo aver creato la struttura di directory, è possibile aggiungere il codice dell'applicazione e i file di configurazione nella directory di codice e file di configurazione. È anche possibile creare ulteriori directory o sottodirectory directory di codice o configurazione.

Servizio tessuti indica un xcopy del contenuto di directory principale dell'applicazione, quindi non esiste alcun struttura predefinita da utilizzare ad eccezione di creazione di due directory superiore, codice e impostazioni. (È possibile selezionare nomi diversi se si desidera. Per ulteriori informazioni sono disponibili nella sezione successiva.)

>[AZURE.NOTE] Assicurarsi che includono tutti i file/dipendenze dell'applicazione. Servizio tessuti consente di copiare il contenuto del pacchetto dell'applicazione in tutti i nodi cluster in cui i servizi dell'applicazione sta per essere distribuito. Il pacchetto deve contenere tutto il codice per poter eseguire l'applicazione. Non è consigliabile presupponendo che le dipendenze sono già installate.

### <a name="edit-the-service-manifest-file"></a>Modificare il file manifesto servizio
Il passaggio successivo consiste nel modificare il file manifesto servizio per includere le seguenti informazioni:

- Il nome del tipo di servizio. Si tratta di un ID servizio tessuti vengono utilizzati per identificare un servizio.
- Il comando da utilizzare per avviare l'applicazione (ExeHost).
- Tutti gli script che deve essere eseguita per impostare freccia su/configurare l'applicazione (SetupEntrypoint).

Di seguito è un esempio di un `ServiceManifest.xml` file:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

In questo articolo verranno illustrate le diverse parti del file che è necessario aggiornare:

#### <a name="updating-the-servicetypes"></a>Aggiornamento di ServiceTypes

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

- È possibile selezionare un nome che si desidera per `ServiceTypeName`. Cui viene utilizzato il valore di `ApplicationManifest.xml` file per identificare il servizio.
- È necessario specificare `UseImplicitHost="true"`. Questo attributo indica tessuti servizio che il servizio è basato su un'applicazione autonoma, in modo che tutte le esigenze di servizio tessuti da eseguire è per avviare un processo e monitorare l'integrità.

#### <a name="updating-the-codepackage"></a>Aggiornamento di CodePackage
L'elemento CodePackage specifica il percorso e versione, di codice del servizio.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

Il `Name` elemento viene utilizzato per specificare il nome della directory nel pacchetto dell'applicazione che contiene il codice del servizio. `CodePackage`è installato anche il `version` attributo. Questa operazione può essere utilizzata per specificare la versione del codice e anche potenzialmente può essere utilizzata per aggiornare il codice del servizio mediante l'infrastruttura di gestione del ciclo di vita dell'applicazione del servizio tessuti.
#### <a name="optional-updating-the-setupentrypoint"></a>Facoltativo: Aggiornamento di SetupEntrypoint

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
L'elemento SetupEntrypoint viene utilizzato per specificare eventuali file eseguibile o batch che deve essere eseguito prima del servizio viene diffuso. È un passaggio facoltativo, in modo che non sia se non esiste nessuna inizializzazione/installazione necessaria. Il SetupEntryPoint viene eseguita ogni volta che il riavvio del servizio.

Esiste un solo SetupEntrypoint, in modo che gli script di installazione/configurazione devono essere raggruppati in un file batch solo se il programma di installazione o configurazione dell'applicazione richiede più script. Il SetupEntrypoint possono eseguire qualsiasi tipo di file, ossia file eseguibili, file batch e i cmdlet di PowerShell. Per ulteriori informazioni, vedere [Configurare SetupEntryPoint](service-fabric-application-runas-security.md).

Nell'esempio precedente, il SetupEntrypoint viene eseguito un file batch denominato `LaunchConfig.cmd` che si trova nel `scripts` sottodirectory della directory di codice (presupponendo che l'elemento WorkingFolder è impostato su base di codice).

#### <a name="updating-the-entrypoint"></a>Aggiornare il punto di ingresso

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

Il `Entrypoint` elemento nel file manifesto del servizio viene utilizzato per specificare la modalità di avvio del servizio. Il `ExeHost` elemento specifica il file eseguibile e argomenti, che deve essere utilizzato per avviare il servizio.

- `Program`Specifica il nome del file eseguibile da eseguire per avviare il servizio.
- `Arguments`Specifica gli argomenti da passare al file eseguibile. Può trattarsi di un elenco dei parametri con argomenti.
- `WorkingFolder`Specifica la cartella di lavoro per il processo che sta per essere avviato. È possibile specificare tre valori:
    - `CodeBase`Specifica che la cartella di lavoro verrà impostata a directory del codice nel pacchetto dell'applicazione (`Code` directory nella struttura di file precedente).
    - `CodePackage`Specifica che la cartella di lavoro verrà impostata per la radice del pacchetto dell'applicazione (`GuestService1Pkg` nella struttura di file precedente).
  - `Work`Specifica che i file vengono inseriti in una sottodirectory denominata lavoro

Il WorkingFolder è utile per impostare la cartella di lavoro corretti in modo che i percorsi relativi possono essere usati dal script dell'applicazione o inizializzazione.

#### <a name="updating-the-endpoints-and-registering-with-naming-service-for-communication"></a>Aggiornare i punti finali e la registrazione con il servizio di denominazione per le comunicazioni

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
Nell'esempio precedente la `Endpoint` elemento specifica gli endpoint che possa ascolto dell'applicazione. In questo esempio, l'applicazione Node in ascolto su http su porta 3000.

È inoltre possibile chiedere tessuti servizio per pubblicare l'endpoint del servizio per la denominazione in modo che altri servizi di individuare l'indirizzo dell'endpoint al servizio. In questo modo è la possibilità di comunicare tra i servizi che sono eseguibili guest.
L'indirizzo dell'endpoint pubblicato ha il formato `UriScheme://IPAddressOrFQDN:Port/PathSuffix`. `UriScheme`e `PathSuffix` sono attributi facoltativi. `IPAddressOrFQDN`è l'indirizzo IP o il nome di dominio completo del nodo questo eseguibile viene collocato nella e viene calcolato automaticamente.

Nell'esempio seguente dopo il servizio viene distribuito, in Esplora aree tessuti di servizio viene visualizzato un endpoint simile a `http://10.1.4.92:3000/myapp/` pubblicati per l'istanza del servizio o se si tratta di un computer locale viene visualizzato `http://localhost:3000/myapp/`. 

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```
È possibile usare questi indirizzi con [proxy inverso](service-fabric-reverseproxy.md) per le comunicazioni tra i servizi.

### <a name="edit-the-application-manifest-file"></a>Modificare il file manifesto applicazione

Dopo aver configurato il `Servicemanifest.xml` file, è necessario apportare alcune modifiche relative alla `ApplicationManifest.xml` file per assicurarsi che il tipo di servizio corretto e il nome utilizzati.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport

Nel `ServiceManifestImport` elemento, è possibile specificare uno o più servizi che si desidera includere nell'app. Servizi fanno riferimento a `ServiceManifestName`, che specifica il nome della directory in cui il `ServiceManifest.xml` file si trova.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Configurare la registrazione
Per gli eseguibili guest, è utile per essere in grado di vedere i registri di console per sapere se gli script di configurazione e applicazione mostrano gli errori.
Può essere configurata reindirizzamento della console di `ServiceManifest.xml` file mediante la `ConsoleRedirection` elemento.

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

* `ConsoleRedirection`può essere utilizzato per reindirizzare l'output di console (stdout e stderr) a una cartella di lavoro in modo che possono essere utilizzati per verificare che non siano presenti errori durante l'installazione o l'esecuzione dell'applicazione del cluster tessuti servizio.

    * `FileRetentionCount`Determina il numero di file vengono salvato nella cartella di lavoro. Il valore 5, ad esempio, significa che i file di log per le cinque esecuzioni precedente sono archiviati nella cartella di lavoro.
    * `FileMaxSizeInKb`Consente di specificare le dimensioni massime dei file di log.

File di log vengono salvati in una directory di lavoro del servizio. Per determinare dove si trovano i file, è necessario utilizzare servizio tessuti Explorer per determinare il nodo che il servizio è in esecuzione e viene utilizzata la cartella di lavoro. Questo processo viene descritto più avanti in questo articolo.

## <a name="deployment"></a>Distribuzione
L'ultimo passaggio consiste nel distribuire l'applicazione. Script di PowerShell seguente viene illustrato come distribuire l'applicazione al cluster di sviluppo locale e iniziare un nuovo servizio di assistenza tessuti.

```PowerShell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```
Un servizio di assistenza tessuti può essere distribuito in varie "configurazioni". Ad esempio, può essere distribuito come unica o più istanze o possono essere distribuito in modo che vi è un'istanza del servizio su ciascun nodo del cluster servizio tessuti.

Il `InstanceCount` parametro del `New-ServiceFabricService` cmdlet consente di specificare quante istanze del servizio devono essere avviate cluster servizio tessuti. È possibile impostare il `InstanceCount` valore, a seconda del tipo di applicazione che si desidera distribuire. I due scenari più comuni:

* `InstanceCount = "1"`. In questo caso, solo un'istanza del servizio viene distribuita in cluster. Utilità di pianificazione del servizio tessuti determina quale nodo il servizio sta per essere distribuito su.

* `InstanceCount ="-1"`. In questo caso, un'istanza del servizio viene distribuita in ogni nodo cluster servizio tessuti. Il risultato è possibile istanza (un unico) del servizio per ogni nodo del cluster.

Si tratta di una configurazione utile per applicazioni front-end (ad esempio, un endpoint resto) perché le applicazioni client devono connettersi "" a uno qualsiasi dei nodi del cluster di utilizzare il punto finale. Questa configurazione può essere utilizzata anche quando, ad esempio, tutti i nodi del cluster tessuti servizio esegue la connessione a un servizio di bilanciamento del carico per consentire il traffico client può essere distribuito attraverso il servizio è in esecuzione su tutti i nodi del cluster.

## <a name="check-your-running-application"></a>Verificare l'applicazione in esecuzione

In Esplora tessuti servizio, identificare il nodo in cui viene eseguito il servizio. In questo esempio viene eseguito sul nodo 1:

![Nodo in cui il servizio è in esecuzione](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Passare al livello di nodo e passare all'applicazione visualizzate le informazioni essenziali nodo, incluso il percorso su disco.

![Percorso su disco](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Se si passa alla directory tramite Esplora Server, è possibile trovare la cartella di lavoro e cartella log del servizio come illustrato nell'immagine seguente.

![Posizione del Registro](./media/service-fabric-deploy-existing-app/loglocation.png)


## <a name="next-steps"></a>Passaggi successivi
In questo articolo sono appreso come includere un eseguibile guest e distribuirlo a infrastruttura di servizio. Passaggio successivo, è possibile estrarre contenuto aggiuntivo per questo argomento.

- [Esempio di confezioni per i prodotti e distribuzione guest eseguibile su GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/GuestExe/SimpleApplication), incluso un collegamento a non definitiva dello strumento
- [Distribuire più eseguibili guest](service-fabric-deploy-multiple-apps.md)
- [Creare un'applicazione di servizio tessuti prima utilizzando Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
