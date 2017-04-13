<properties
   pageTitle="Utilizzando Elasticsearch come un archivio di traccia dell'applicazione di servizio tessuti | Microsoft Azure"
   description="Descrive come applicazioni di servizio tessuti possono utilizzare Elasticsearch e Kibana per archiviare, indice ed effettuare ricerche tramite tracce di applicazione (registri)"
   services="service-fabric"
   documentationCenter=".net"
   authors="karolz-ms"
   manager="adegeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/09/2016"
   ms.author="karolz@microsoft.com"/>

# <a name="use-elasticsearch-as-a-service-fabric-application-trace-store"></a>Utilizzare Elasticsearch come una traccia di applicazione di servizio tessuti archiviare
## <a name="introduction"></a>Introduzione
Questo articolo descrive come le applicazioni di [Azure servizio tessuti](https://azure.microsoft.com/documentation/services/service-fabric/) possono utilizzare **Elasticsearch** e **Kibana** per lo spazio di archiviazione di applicazione traccia, indicizzazione e ricerca. [Elasticsearch](https://www.elastic.co/guide/index.html) è un open source, distribuito e scalabilità in tempo reale analitica e ricerca motore sono adatti per questa operazione. Può essere installato su Windows e Linux macchine virtuali in esecuzione in Microsoft Azure. Elasticsearch può elaborare in modo efficiente *strutturati* tracce prodotte utilizzando tecnologie, ad esempio **Individua eventi di Windows (ETW)**.

ETW viene utilizzato dal servizio tessuti runtime per informazioni di diagnostica origine (tracce). È il metodo consigliato per le applicazioni di servizio tessuti di origine troppo le informazioni di diagnostiche. Utilizzando lo stesso meccanismo consente di correlazione tra le tracce di runtime fornito e fornito dall'applicazione e rende più facile con la risoluzione dei problemi. Modelli di progetto tessuti servizio Visual Studio includono una API di registrazione (in base della classe .NET **EventSource** ) che genera tracce ETW per impostazione predefinita. Per informazioni generali su analisi di applicazione di servizio tessuti tramite ETW, vedere [monitoraggio e la diagnosi servizi in una configurazione di sviluppo di computer locale](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

Per le tracce compaiano Elasticsearch devono acquisite i nodi di cluster tessuti assistenza in tempo reale (mentre l'applicazione è in esecuzione) e inviato a un endpoint Elasticsearch. Sono disponibili due opzioni principali per l'acquisizione di traccia:

+ **Acquisizione di traccia in corso**  
Applicazione o con maggiore precisione processo del servizio, è responsabile per l'invio di dati di diagnostica per l'archivio di traccia (Elasticsearch).

+ **Acquisizione di traccia di fuori del processo**  
Un agente distinto è tracce dal processo di servizio o i processi di acquisizione e inviarli all'archivio di analisi.

Sotto, si viene descritto come configurare Elasticsearch su Azure, discutere i professionisti e contro entrambi opzioni di acquisizione e viene illustrato come configurare un servizio di assistenza tessuti per inviare dati a Elasticsearch.


## <a name="set-up-elasticsearch-on-azure"></a>Impostare Elasticsearch su Azure
Il modo più semplice per configurare il servizio Elasticsearch su Azure è tramite [**Gestione risorse di Azure modelli**](../azure-resource-manager/resource-group-overview.md). Un [modello di gestione risorse Azure Guida introduttiva per Elasticsearch](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch) completa è disponibile dal repository modelli Guida introduttiva di Azure. Questo modello utilizza gli account di archiviazione separata per le unità della scala (gruppi di nodi). Anche possibile eseguire il provisioning client distinto e nodi di server con configurazioni diverse e vari numeri di dischi dati collegati.

Qui, si utilizza un altro modello, denominato **ES MultiNode** dal [repository Azure strumenti di diagnostica](https://github.com/Azure/azure-diagnostics-tools). Questo modello è più semplice da utilizzare e viene creato un cluster di Elasticsearch protetto tramite l'autenticazione di base HTTP. Prima di procedere, scaricare l'archivio dal GitHub nel computer in uso (duplicazione repository o scaricare un file zip). Il modello ES MultiNode si trova nella cartella con lo stesso nome.

### <a name="prepare-a-machine-to-run-elasticsearch-installation-scripts"></a>Preparare una macchina per l'esecuzione di script di installazione Elasticsearch
Il modo più semplice per utilizzare il modello ES MultiNode consiste nell'usare uno script di PowerShell Azure fornito chiamato `CreateElasticSearchCluster`. Per usare questo script, è necessario installare uno strumento denominato **openssl**e moduli. Quest'ultimo è necessaria per la creazione di una chiave SSH che può essere utilizzata per amministrare il cluster Elasticsearch in modalità remota.

`CreateElasticSearchCluster`script è progettato per facilitare l'utilizzo con il modello ES MultiNode da un computer Windows. È possibile utilizzare il modello in un computer non Windows, ma questo scenario è lo scopo di questo articolo.

1. Se non è stato installato loro già, installare [**PowerShell Azure moduli**](http://aka.ms/webpi-azps). Quando richiesto, fare clic su **Esegui**, quindi **installare**. È necessario Azure PowerShell 1.3 o versione successiva.

2. Lo strumento **openssl** è incluso nella distribuzione di [**Operazioni per Windows**](http://www.git-scm.com/downloads). Se non è già stato fatto, installare ora [Fra per Windows](http://www.git-scm.com/downloads) . (Le opzioni di installazione predefinite sono OK).

3. Supponendo che fra è stato installato ma non incluso nel percorso di sistema, aprire una finestra di Microsoft Azure PowerShell ed eseguire i comandi seguenti:

    ```powershell
    $ENV:PATH += ";<Git installation folder>\usr\bin"
    $ENV:OPENSSL_CONF = "<Git installation folder>\usr\ssl\openssl.cnf"
    ```

    Sostituire il `<Git installation folder>` con il percorso fra nel computer in uso; il valore predefinito è **"c:\Programmi\Microsoft Files\Git"**. Nota il punto e virgola all'inizio del primo percorso.

4. Verificare che si è connessi a Azure (tramite [`Add-AzureRmAccount`](https://msdn.microsoft.com/library/mt619267.aspx) cmdlet) e di avere selezionato l'abbonamento a cui deve essere utilizzato per creare il cluster di ricerca flessibile. È possibile verificare che abbonamento corretto sia selezionata utilizzando `Get-AzureRmContext` e `Get-AzureRmSubscription` cmdlet.

5. Se già fatto, è possibile cambiare la directory corrente nella cartella MultiNode ES.

### <a name="run-the-createelasticsearchcluster-script"></a>Eseguire lo script CreateElasticSearchCluster
Prima di eseguire lo script, aprire la `azuredeploy-parameters.json` file e verificare o fornire i valori per i parametri dello script. Sono disponibili i seguenti:

|Nome parametro           |Descrizione|
|-----------------------  |--------------------------|
|dnsNameForLoadBalancerIP |Il nome che viene utilizzato per creare il nome DNS pubblicamente visibile per la ricerca flessibile cluster (mediante l'aggiunta del dominio area Azure al nome fornito). Ad esempio, se il valore del parametro è "myBigCluster" e l'area di Azure scelto è occidentale degli Stati Uniti, il nome DNS risultante per il cluster è myBigCluster.westus.cloudapp.azure.com. <br /><br />Questo nome funge anche da un nome radice per tutti gli elementi associati cluster flessibile ricerca, ad esempio i nomi dei nodi di dati.|
|adminUsername           |Il nome dell'account dell'amministratore per la gestione del cluster di ricerca flessibile (chiavi SSH corrispondenti vengono generate automaticamente).|
|dataNodeCount           |Il numero dei nodi del cluster di ricerca flessibile. La versione corrente dello script non rileva la differenza tra nodi di dati e alle query. tutti i nodi riprodurre entrambi i ruoli. Valore predefinito è 3 nodi.|
|dataDiskSize            |Le dimensioni di dati dischi (GB) da allocare per ogni nodo di dati. Ogni nodo riceve 4 dischi di dati, in modo esclusivo dedicati al servizio di ricerca flessibile.|
|area geografica                  |Il nome dell'area Azure in cui il cluster di ricerca flessibile deve trovarsi.|
|esUserName              |Il nome utente dell'utente che è configurato per accedere a cluster ES (soggetto all'autenticazione di base HTTP). La password non fa parte del file dei parametri e deve essere fornita quando `CreateElasticSearchCluster` richiamato.|
|vmSizeDataNodes         |Le dimensioni di Azure macchina virtuale per i nodi cluster ricerca flessibile. Valore predefinito è Standard_D2.|

A questo punto si è pronti eseguire lo script. Il comando seguente:

```powershell
CreateElasticSearchCluster -ResourceGroupName <es-group-name> -Region <azure-region> -EsPassword <es-password>
```

dove 

|Nome parametro script    |Descrizione|
|-----------------------  |--------------------------|
|`<es-group-name>`        |Il nome del gruppo di risorse Azure contenenti tutte le risorse cluster di ricerca flessibile.|
|`<azure-region>`         |Il nome dell'area di Azure in cui deve essere creato cluster ricerca flessibile.|         
|`<es-password>`          |La password per l'utente ricerca flessibile.|

>[AZURE.NOTE] Se si riceve una NullReferenceException da cmdlet Test AzureResourceGroup, si è dimenticata ad accedere a Azure (`Add-AzureRmAccount`).

Se viene visualizzato un messaggio di errore eseguendo lo script e si stabilisce che l'errore è stato causato da un valore di parametro modello errato, correggere il file di parametro ed eseguire nuovamente lo script con il nome di un gruppo di risorse diversi. È possibile inoltre riutilizzare lo stesso nome gruppo di risorse e lo script pulire il vecchio aggiungendo il `-RemoveExistingResourceGroup` parametro per la chiamata di script.

### <a name="result-of-running-the-createelasticsearchcluster-script"></a>Risultati dell'esecuzione dello script CreateElasticSearchCluster
Dopo avere eseguito la `CreateElasticSearchCluster` script, verranno creati i seguenti elementi principali. In questo esempio si presuppone che sia stata utilizzata "myBigCluster" come valore della `dnsNameForLoadBalancerIP` parametro e che l'area geografica in cui è stato creato il cluster sia Stati Uniti ovest.

|Elemento|Nome, percorso e note|
|----------------------------------|----------------------------------|
|Chiave SSH per l'amministrazione remota |file di myBigCluster.key (nella directory da cui è stato eseguito il CreateElasticSearchCluster). <br /><br />In questo file chiave può essere utilizzato per connettersi al livello di nodo Amministrazione e (tramite il nodo Amministrazione) per nodi di dati del cluster.|
|Nodo Amministrazione                        |myBigCluster admin.westus.cloudapp.azure.com <br /><br />Una macchina virtuale dedicata per Elasticsearch cluster l'amministrazione remota, ossia l'unico che consente connessioni SSH esterne. Viene eseguito nella stessa rete virtuale di tutti i nodi di cluster Elasticsearch, ma non viene eseguito uno o più servizi Elasticsearch.|
|Nodi di dati                        |myBigCluster1... myBigCluster*N* <br /><br />Nodi di dati che eseguono servizi Elasticsearch e Kibana. È possibile connettersi tramite SSH a ciascun nodo, ma solo tramite il nodo Amministrazione.|
|Elasticsearch cluster             |http://myBigCluster.westus.cloudapp.Azure.com/es/ <br /><br />Endpoint primario per il cluster Elasticsearch (notare il suffisso /es). È protetto tramite l'autenticazione di base HTTP (le credenziali sono i parametri specificati esUserName/esPassword del modello ES MultiNode). Il cluster dispone anche intestazione plug-in installato (http://myBigCluster.westus.cloudapp.azure.com/es/_plugin/head) per l'amministrazione del cluster di base.|
|Servizio Kibana                    |http://myBigCluster.westus.cloudapp.Azure.com <br /><br />Il servizio Kibana sia configurato per mostrare i dati dal cluster Elasticsearch creato. È protetto tramite le stesse credenziali di autenticazione cluster stesso.|

## <a name="in-process-versus-out-of-process-trace-capturing"></a>In corso e l'acquisizione di traccia di fuori del processo
Introduzione, sono indicati in due modi principali per la raccolta di dati di diagnostica: in corso e in uscita del processo. Ogni presenta vantaggi e svantaggi.

Vantaggi di **acquisizione di traccia in corso** :

1. *Distribuzione e configurazione semplice*

    * La configurazione della raccolta di dati di diagnostica è solo una parte della configurazione dell'applicazione. È facile sempre mantenere "aggiornati" con il resto dell'applicazione.

    * È facile gestione configurazione per applicazione o al servizio.

    * In genere l'acquisizione di traccia di fuori del processo richiede una distribuzione separata e alla configurazione dell'agente di diagnostica, è un'attività amministrativa aggiuntivi e un potenziale degli errori. La tecnologia agente specifico consente spesso solo un'istanza di agente di ogni macchina virtuale (nodo). Questo significa che la configurazione per la raccolta della configurazione di diagnostica è condiviso tra tutte le applicazioni e servizi in esecuzione su tale nodo.

2. *Flessibilità*

    * L'applicazione può inviare i dati nel punto in cui deve essere inviato, purché non esiste una libreria client che supporta il sistema di archiviazione di dati. Nuovo sink possono essere aggiunti come desiderato.

    * È possibile implementare le regole di acquisizione, filtro e aggregazione di dati complesse.

    * Una traccia di fuori del processo di acquisizione è spesso limitato pozzi dati che supporta l'agente. Alcuni agenti sono extensible.

3. *Accesso ai dati interno dell'applicazione e contesto*

    * Sottosistema diagnostico in esecuzione all'interno del processo di applicazione/servizio possibile integrare facilmente le tracce con informazioni contestuali.

    * Nell'approccio fuori del processo, è necessario inviare i dati a un agente tramite un meccanismo di comunicazione tra processi, ad esempio traccia eventi per Windows. Questo meccanismo imporre limitazioni aggiuntive.

Vantaggi di **acquisizione di traccia di fuori del processo** :

1. *La possibilità di eseguire il monitoraggio delle applicazioni e raccogliere anomalo*

    * Traccia in corso l'acquisizione potrebbe non riuscire se l'applicazione non è possibile avviare o si blocca. Un agente indipendente è maggiori possibilità di raccogliere tutte le informazioni essenziali sulla risoluzione dei problemi.<br /><br />

2. *Scad, affidabilità e le prestazioni consolidate*

    * Un agente sviluppato da un fornitore di piattaforma (ad esempio un agente di diagnostica Microsoft Azure) è stata soggetto a accurata test e protezione avanzata battaglia.

    * Con traccia in corso l'acquisizione, è necessario prestare attenzione per garantire che l'attività di invio di dati di diagnostica da un processo dell'applicazione non interferire con le attività principale dell'applicazione o causare problemi di prestazioni o intervallo. Un agente in modo indipendente in esecuzione è meno soggetto a questi problemi e progettato specificamente per limitare l'impatto sul sistema.

È possibile combinare e trarre vantaggio da entrambi gli approcci. In effetti, potrebbe essere la soluzione ideale per molte applicazioni.

Qui, si utilizza la **Raccolta Microsoft.Diagnostic.Listeners** e la traccia in corso l'acquisizione per inviare i dati da un'applicazione di servizio tessuti a un cluster di Elasticsearch.

## <a name="use-the-listeners-library-to-send-diagnostic-data-to-elasticsearch"></a>Utilizzare la libreria di listener per inviare i dati di diagnostica per Elasticsearch
Raccolta Microsoft.Diagnostic.Listeners fa parte di applicazione di servizio tessuti esempio PartyCluster. Per utilizzare:

1. Scaricare [l'esempio PartyCluster](https://github.com/Azure-Samples/service-fabric-dotnet-management-party-cluster) da GitHub.

2. Copiare la cartella della soluzione dell'applicazione che dovrebbe essere per inviare i dati a Elasticsearch Microsoft.Diagnostics.Listeners e Microsoft.Diagnostics.Listeners.Fabric progetti (intere cartelle) dalla directory dell'esempio PartyCluster.

3. Aprire la soluzione di destinazione, rapida nodo della soluzione in Esplora soluzioni e scegliere **Aggiungi progetto esistente**. Aggiungere il progetto Microsoft.Diagnostics.Listeners alla soluzione. Ripetere la stessa per il progetto Microsoft.Diagnostics.Listeners.Fabric.

4. Aggiungere due progetti aggiunti un riferimento al progetto dai progetti di servizio. (Ogni servizio che dovrebbe essere per inviare dati a Elasticsearch deve fare riferimento Microsoft.Diagnostics.EventListeners e Microsoft.Diagnostics.EventListeners.Fabric).

    ![Riferimenti al progetto alle librerie Microsoft.Diagnostics.EventListeners e Microsoft.Diagnostics.EventListeners.Fabric][1]

### <a name="service-fabric-general-availability-release-and-microsoftdiagnosticstracing-nuget-package"></a>Disponibilità servizio tessuti generale della versione e pacchetto Microsoft.Diagnostics.Tracing Nuget
Applicazioni create con disponibilità servizio tessuti generale della versione (2.0.135, rilasciato 31 marzo 2016) come destinazione **.NET Framework 4.5.2**. Questa versione è la versione più recente di .NET Framework supportati da Azure al momento del rilascio GA. In questa versione di framework Purtroppo non dispone alcune APIs EventListener che deve essere raccolta Microsoft.Diagnostics.Listeners. Poiché EventSource (il componente alla base della registrazione API in applicazioni tessuti) ed EventListener accoppiati, ogni progetto che utilizza la libreria Microsoft.Diagnostics.Listeners necessario utilizzare un'implementazione alternativa del EventSource. Questa implementazione viene fornita dal **pacchetto Microsoft.Diagnostics.Tracing Nuget**, creati da Microsoft. Il pacchetto è completamente compatibile con EventSource inclusi nell'ambito, pertanto alcuna modifica del codice non dovrebbe essere necessario ad eccezione di modifiche di cui viene fatto riferimento dello spazio dei nomi.

Per iniziare a utilizzare l'implementazione di Microsoft.Diagnostics.Tracing della classe EventSource, seguire questa procedura per ogni progetto di servizio che è necessario inviare dati a Elasticsearch:

1. Pulsante destro del mouse sul progetto di servizio e scegliere **Gestisci pacchetti Nuget**.

2. Passare all'origine pacchetto nuget.org (se non è già selezionata) e cercare "**Microsoft.Diagnostics.Tracing**".

3. Installare il `Microsoft.Diagnostics.Tracing.EventSource` pacchetto (e delle dipendenze).

4. Aprire il file **ServiceEventSource.cs** o **ActorEventSource.cs** nel progetto di servizio e sostituire il `using System.Diagnostics.Tracing` direttiva nella parte superiore del file con il `using Microsoft.Diagnostics.Tracing` direttiva.

Questa procedura non sono necessaria una volta **.NET Framework 4.6** è supportato da Microsoft Azure.

### <a name="elasticsearch-listener-instantiation-and-configuration"></a>Configurazione e per creazione istanza: comunicare ascoltatore Elasticsearch
Il passaggio finale per l'invio di dati di diagnostica per Elasticsearch consiste nel creare un'istanza di `ElasticSearchListener` e la configurazione con Elasticsearch connessione dati. Comunicare ascoltatore acquisisce automaticamente tutti gli eventi generati tramite classi EventSource definite nel progetto di servizio. È necessario essere catturati durante la durata del servizio, in modo che il modo migliore per la creazione sia nel codice di inizializzazione del servizio. Ecco come il codice di inizializzazione di un servizio senza stato potrebbe essere dopo le modifiche necessarie (aggiunte sottolineato nei commenti a partire da `****`):

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Runtime;

// **** Add the following directives
using Microsoft.Diagnostics.EventListeners;
using Microsoft.Diagnostics.EventListeners.Fabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>        
        private static void Main()
        {
            try
            {
                // **** Instantiate ElasticSearchListener
                var configProvider = new FabricConfigurationProvider("ElasticSearchEventListener");
                ElasticSearchListener esListener = null;
                if (configProvider.HasConfiguration)
                {
                    esListener = new ElasticSearchListener(configProvider);
                }

                // The ServiceManifest.XML file defines one or more service type names.
                // Registering a service maps a service type name to a .NET type.
                // When Service Fabric creates an instance of this service type,
                // an instance of the class is created in this host process.

                ServiceRuntime.RegisterServiceAsync("Stateless1Type", 
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                // Prevents this host process from terminating so services keep running.
                Thread.Sleep(Timeout.Infinite);

                // **** Ensure that the ElasticSearchListner instance is not garbage-collected prematurely
                GC.KeepAlive(esListener);
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e);
                throw;
            }
        }
    }
}
```

Elasticsearch connessione dati devono essere inseriti in una sezione separata nel file di configurazione del servizio (**PackageRoot\Config\Settings.xml**). Il nome della sezione deve corrispondere al valore passato per il `FabricConfigurationProvider` costruttore, ad esempio:

```xml
<Section Name="ElasticSearchEventListener">
  <Parameter Name="serviceUri" Value="http://myBigCluster.westus.cloudapp.azure.com/es/" />
  <Parameter Name="userName" Value="(ES user name)" />
  <Parameter Name="password" Value="(ES user password)" />
  <Parameter Name="indexNamePrefix" Value="myapp" />
</Section>
```
I valori di `serviceUri`, `userName` e `password` parametri corrispondono all'indirizzo endpoint cluster Elasticsearch, nome utente Elasticsearch e la password, rispettivamente. `indexNamePrefix`è il prefisso per Elasticsearch indici. Raccolta Microsoft.Diagnostics.Listeners crea un nuovo indice per i propri dati ogni giorno.

### <a name="verification"></a>Verifica
Questo è tutto! A questo punto, ogni volta che viene eseguito il servizio, viene avviato l'invio di tracce al servizio Elasticsearch specificato nella configurazione. È possibile verificare la aprendo UI Kibana associata con l'istanza di Elasticsearch di destinazione. In questo esempio, l'indirizzo della pagina è http://myBigCluster.westus.cloudapp.azure.com/. Verificare che l'indicizzazione con il prefisso del nome scelto per il `ElasticSearchListener` istanza effettivamente creato e popolato con i dati.

![Eventi dell'applicazione PartyCluster con Kibana][2]

## <a name="next-steps"></a>Passaggi successivi
- [Ulteriori informazioni sulla diagnosi e il monitoraggio di un servizio di assistenza tessuti](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

<!--Image references-->
[1]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/listener-lib-references.png
[2]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/kibana.png
