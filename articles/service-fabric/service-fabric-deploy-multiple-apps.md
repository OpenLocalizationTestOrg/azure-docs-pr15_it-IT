<properties
   pageTitle="Distribuire un'applicazione di Node che utilizza MongoDB | Microsoft Azure"
   description="Procedura dettagliata su come creare un pacchetto più eseguibili guest per distribuire in un cluster di Azure servizio tessuti"
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
   ms.workload="NA"
   ms.date="10/22/2016"
   ms.author="msfussell;mikhegn"/>


# <a name="deploy-multiple-guest-executables"></a>Distribuire più eseguibili guest

In questo articolo viene illustrato come creare un pacchetto e distribuire più guest eseguibili in Azure servizio tessuti. Per la creazione e la distribuzione di un singolo pacchetto di servizio tessuti leggere come [distribuire guest eseguibile servizio tessuti](service-fabric-deploy-existing-app.md).

Mentre in questa procedura dettagliata viene illustrato come distribuire un'applicazione di un front-end Node che utilizza MongoDB come archivio dati, è possibile applicare la procedura descritta in qualsiasi applicazione che ha installato dipendenze in un'altra applicazione.   

È possibile utilizzare Visual Studio per generare il pacchetto dell'applicazione che contiene più file eseguibili guest. Vedere [Utilizzando Visual Studio per creare un pacchetto di un'applicazione esistente](service-fabric-deploy-existing-app.md#using-visual-studio-to-package-an-existing-executable). Dopo aver aggiunto il primo eseguibile guest, fare clic con il pulsante destro del progetto di applicazione e selezionare **Aggiungi -> nuova infrastruttura di servizio servizio** per aggiungere il secondo progetto eseguibile guest alla soluzione. Nota: Se si sceglie di creare un collegamento all'origine nel progetto di Visual Studio, la compilazione della soluzione di Visual Studio, verrà assicurarsi che sia sempre aggiornato con le modifiche apportate nell'origine pacchetto dell'applicazione. 

## <a name="manually-package-the-multiple-guest-executable-application"></a>Creare manualmente un pacchetto dell'applicazione eseguibile guest più
In alternativa è possibile comprimere manualmente guest eseguibile. Per imballaggio manuale, in questo articolo viene utilizzato lo strumento di imballaggio tessuti servizio, disponibile [all'http://aka.ms/servicefabricpacktool](http://aka.ms/servicefabricpacktool).

### <a name="packaging-the-nodejs-application"></a>Pacchetto dell'applicazione Node
In questo articolo si presuppone che Node non è installato nei nodi del cluster servizio tessuti. Di conseguenza, è necessario aggiungere Node.exe alla directory principale dell'applicazione nodo prima di confezioni per i prodotti. La struttura di directory dell'applicazione node (con Express web framework e motore del modello Jade) dovrebbe essere simile a quella riportata di seguito:

```
|-- NodeApplication
  	|-- bin
        |-- www
  	|-- node_modules
        |-- .bin
        |-- express
        |-- jade
        |-- etc.
  	|-- public
        |-- images
        |-- etc.
  	|-- routes
        |-- index.js
        |-- users.js
  	|-- views
        |-- index.jade
        |-- etc.
  	|-- app.js
  	|-- package.json
  	|-- node.exe
```

Passaggio successivo, si crea un pacchetto di applicazione per l'applicazione di Node. Il codice riportato di seguito viene creato un pacchetto dell'applicazione di servizio tessuti che contiene l'applicazione Node.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Di seguito è una descrizione dei parametri in uso:

- **/Source** punta alla directory dell'applicazione che deve essere incluso.
- **/target** definisce la directory in cui deve essere creato il pacchetto. Questa directory deve essere diversa dalla directory di origine.
- **/AppName** definisce il nome dell'applicazione dell'applicazione esistente. È importante tenere presente che questo ruolo per il nome del servizio nel manifesto e non per il nome dell'applicazione di servizio tessuti.
- **/exe** definisce il file eseguibile che dovrebbe essere in questo caso di avvio servizio tessuti `node.exe`.
- **/ma** definisce l'argomento utilizzato per avviare il file eseguibile. Come Node non è installato, è necessario avviare il server web Node eseguendo tessuti servizio `node.exe bin/www`.  `/ma:'bin/www'`indica lo strumento da usare `bin/ma` come argomento per node.exe.
- **/AppType** definisce il nome del tipo di applicazione di servizio tessuti.

Se si passa alla directory che è stata specificata nel parametro /target, è possibile vedere che lo strumento ha creato un pacchetto di servizio tessuti funziona come illustrato di seguito:

```
|--[yourtargetdirectory]
  	|-- NodeApplication
        |-- C
              |-- bin
              |-- data
              |-- node_modules
              |-- public
              |-- routes
              |-- views
              |-- app.js
              |-- package.json
              |-- node.exe
        |-- config
              |--Settings.xml
        |-- ServiceManifest.xml
  	|-- ApplicationManifest.xml
```
ServiceManifest.xml generato è ora disponibile una sezione che descrive come deve essere avviato il server web Node, come illustrato nel frammento di codice riportata di seguito:

```xml
<CodePackage Name="C" Version="1.0">
    <EntryPoint>
        <ExeHost>
            <Program>node.exe</Program>
            <Arguments>'bin/www'</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
        </ExeHost>
    </EntryPoint>
</CodePackage>
```
In questo esempio, il server web Node ascolta porta 3000, pertanto è necessario aggiornare le informazioni sull'endpoint nel file ServiceManifest.xml come illustrato di seguito.   

```xml
<Resources>
      <Endpoints>
        <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>Pacchetto dell'applicazione MongoDB
Ora che è stata compressa applicazione Node, è possibile procedere e creare un pacchetto MongoDB. Come detto in precedenza, i passaggi che attraversano ora non sono specifici per Node e MongoDB. Infatti, si applicano a tutte le applicazioni che sono state progettate per essere riuniti insieme come un'applicazione di servizio tessuti.  

Per creare un pacchetto MongoDB, si desidera verificare che si pacchetto Mongod.exe e Mongo.exe. Entrambi i file binari risiede nel `bin` directory della directory di installazione MongoDB. La struttura di directory simile a quella riportata di seguito.

```
|-- MongoDB
  	|-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
Servizio tessuti devono iniziare MongoDB con un comando simile a quella riportata di seguito, pertanto è necessario utilizzare la `/ma` parametro quando imballaggio MongoDB.

```
mongod.exe --dbpath [path to data]
```
> [AZURE.NOTE] I dati non vengano vengono mantenuti nel caso di un errore di nodo se si inserisce nella directory di dati MongoDB nella directory locale del nodo. Usare lo spazio di archiviazione permanente o implementare una replica MongoDB imposta per evitare perdite di dati.  

In PowerShell o shell dei comandi, è eseguire lo strumento di confezioni per i prodotti con i parametri seguenti:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Per aggiungere il pacchetto di applicazione di servizio tessuti MongoDB, è necessario assicurarsi che i punti di parametro /target nella stessa directory che contiene già l'applicazione manifesto insieme all'applicazione Node. Inoltre, è necessario verificare che si usa lo stesso nome ApplicationType.

Di seguito, passare alla directory ed esaminare lo strumento creato.

```
|--[yourtargetdirectory]
  	|-- MyNodeApplication
  	|-- MongoDB
        |-- C
            |--bin
                |-- mongod.exe
                |-- mongo.exe
                |-- etc.
        |-- config
            |--Settings.xml
        |-- ServiceManifest.xml
  	|-- ApplicationManifest.xml
```
Come si può notare, lo strumento aggiunta una nuova cartella, MongoDB, alla directory contenente i file binari MongoDB. Se si apre la `ApplicationManifest.xml` file, è possibile visualizzare il pacchetto contiene ora applicazione Node sia MongoDB. Il codice riportato di seguito mostra il contenuto del manifesto dell'applicazione.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyNodeApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MongoDB" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeService" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MongoDBService">
         <StatelessService ServiceTypeName="MongoDB">
            <SingletonPartition />
         </StatelessService>
      </Service>
      <Service Name="NodeServiceService">
         <StatelessService ServiceTypeName="NodeService">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
</ApplicationManifest>  
```

### <a name="publishing-the-application"></a>L'applicazione di pubblicazione
L'ultimo passaggio consiste nel pubblicare l'applicazione in cluster tessuti servizio locale usando gli script di PowerShell riportata di seguito:

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Dopo l'applicazione viene pubblicato correttamente in cluster locale, è possibile accedere all'applicazione Node sulla porta che sono state immesse nella manifesto servizio dell'applicazione Node, ad esempio http://localhost:3000.

In questa esercitazione, è stato illustrato come creare package due applicazioni esistente come un'applicazione di servizio tessuti. Inoltre appreso come distribuirlo a infrastruttura di servizio in modo che possono trarre vantaggio da alcune funzionalità dell'infrastruttura di servizio, ad esempio disponibilità e l'integrazione di integrità del sistema.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulla distribuzione contenitori con [Panoramica dell'infrastruttura di servizio e contenitori](service-fabric-containers-overview.md)
